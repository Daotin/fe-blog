---
layout: mypost
title: vite 项目为什么可以直接使用 NODE_ENV？
tags: vite
---

## 背景

我们知道，在`process.env`中并没有`NODE_ENV`这个变量，但是我们却可以在项目的代码中使用 process.env.NODE_ENV 这个值来判断当前是 development 环境还是 production 环境，然后进行后续的逻辑操作。

这说明，在 vite 内部，对 process.env.NODE_ENV 有赋值的操作，但是在公司项目中，启动的时候不管是 serve 还是 build，在在`tailwind.config.js`中打印 process.env.NODE_ENV 变量，`NODE_ENV`均为`development`，这就让人感觉很困惑。

当时为了简单处理，使用了行内 npm scrips 配置，即在启动服务的时候，设置 process.env.NODE_ENV 的值，如下所示：

```javascript
"scripts": {
  "serve": "cross-env NODE_ENV=development vite",
  "build": "cross-env NODE_ENV=prod vite build --mode prod",
  "build-v1": "cross-env NODE_ENV=v1 vite build --mode v1",
},
```

后来一直觉得不妥，应该有更为简单合理的方式去判断当前项目所处的环境，毕竟 vite 中会有`.env`文件来让我们配置环境变量，应该跟 process.env.NODE_ENV 有些许关系。

于是，决定深入 vite 源码去剖析 process.env.NODE_ENV 赋值的逻辑，最后找到了更好的替代 cross-env 的方式去判断当前项目所处的环境。也是这篇文章的动机所在。

## 分析执行过程

首先，在一些需要的地方打上断点：

- vite 启动位置 defineConfig

- tailwind.config.js 文件开始位置

- vite 源码中，对 process.env.NODE_ENV 进行赋值的位置

然后在 VSCode 中，配置好 launch.json 后，启动调试。

由于我们执行的是 npm run serve，所以首先会断在 cli.js 中，创建服务的位置，可以看到调用`createServer`创建本地服务。

![](/image/image_q4v_XXEBsG.png)

继续 F5，进入`createServer`，会执行`resolveConfig`，其中参数`inlineConfig.mode` 就是我们 npm scrips 中指定的 mode，这里没有指定（执行的只是 npm run serve: vite）mode，所以这里为 undefined。

![](/image/image_hV-XAHDpdP.png)

经过一次判断 `mode === production`，不成立，此时 process.env.NODE_ENV 还是 undefined。

> （在 build 时候，由于我是手动指定 mode 为 prod，导致`prod ≠= production`，所以`process.env.NODE_ENV`还是 undefined。）

进入`loadEnv`方法，这里 vite 会读取`.env`文件，然后，如果是“VITE \_”开头，会存入 env 变量中，如果是“NODE_ENV”变量，会设置`process.env.VITE_USER_NODE_ENV = value; `即设置的 NODE_ENV 的值。（这个`process.env.VITE_USER_NODE_ENV`就可以为我们所用）

![](/image/image_m-XhuQ9psq.png)

继续往下 F5，`loadEnv`完后，继续一个判断：因为我们在`.env`文件中没有定义 NODE_ENV 变量，所以`isProduction`是 false，所以这片代码过后，`process.env.NODE_ENV`还是 undefined。

![](/image/image_yFWMQxnuWk.png)

继续 F5，然后会走到一个`createContext`函数，这个就是关键所在。由于我们的`process.env.NODE_ENV`在之前一直为 undefined，所以 ctx.env 就是 undefined，于是`process.env.NODE_ENV`被赋值为 development。

![](/image/image_xHJ0uyyGMp.png)

我们继续，经过多次`createContext`函数的进进出出，最后终于到了 tailwind 文件中，可以看到 NODE_ENV 的值为 development。

![](/image/image_1GCggjsTOm.png)

这也是为什么不管是 serve 还是 build，我们一直在`tailwind.config.js`中打印的 NODE_ENV 都是 development 的原因。

我之前还一直以为，是由于`tailwind.config.js`会在`vite.config.js`之前执行。

## 解决方案

之前也想在 tailwind 文件中使用`import.meta.env`来获取`VITE_`开头的环境变量，因为加载的.env 中的环境变量也会通过 `import.meta.env` 以字符串形式暴露给客户端源码。

但是只要在 tailwind 中一引用就会报错。

最后通过查询才知道，`import.meta` 是一个内置在 ES 模块内部的对象（之前一直以为是 vite 暴露出来的）。而我们的 tailwind 文件是 CommonJS 模块，自然没有`import.meta`对象。

所以此路不通，得想其他的办法。

之前提到，在`.env`文件中定义的`NODE_ENV`，会在 vite 中被赋值给`process.env.VITE_USER_NODE_ENV`，我们可以用`VITE_USER_NODE_ENV`来作为我们项目环境的判断。

只需在`.env`文件中加上`NODE_ENV`变量：

```javascript
NODE_ENV = prod;
```

然后在代码中使用`VITE_USER_NODE_ENV`来进行判断即可：

```javascript
purge: {
  // 仅生产环境启用优化
  enabled: process.env.VITE_USER_NODE_ENV == 'prod',
  // 删除未使用的CSS tree-shake，减少打包后的体积
  content: ['./src/**/*.html', './src/**/*.vue', './src/**/*.jsx'],
},
```

（完）
