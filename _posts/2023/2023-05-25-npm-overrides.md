---
layout: mypost
title: npm依赖的子依赖升级导致项目报错怎么办？用overrides解决
tags: npm
---

## 背景

今天早上刚上班，后收到测试发来的一张图，测试环境构建异常，报了一个错误。

![](/image/2023/1.png)

具体错误的位置是：

```
require() of /root/.jenkins/workspace/1475-001-build-test-html-nginx/app/node_modules/brilliant-errors/node_modules/callsites/index.js from /root/.jenkins/workspace/1475-001-build-test-html-nginx/app/node_modules/brilliant-errors/dist/index.cjs is an ES module file as it is a .js file whose nearest parent package.json contains "type": "module" which defines all .js files in that package scope as ES modules.

Instead rename index.js to end in .cjs, change the requiring code to use import(), or remove "type": "module" from /root/.jenkins/workspace/1475-001-build-test-html-nginx/app/node_modules/brilliant-errors/node_modules/callsites/package.json.
```

但是本地并没有复现，于是从仓库拉了最新的代码，在本地构建打包，果然出现了上面的问题。

## 问题分析

既然之前本地没有问题，重新构建的有问题，肯定是安装的包不同了，但是因为 `package.json` 是相同的，那么肯定是 `package-lock.json` 是不同的，对比了下果然有很大的差异。

因为差异太大，所以就从报错信息入手，找到了 `callsites` 这个包，经过对比，确实不同。那么只需要锁定之前的版本就好了，于是我就想，从 callsites 入手，一直往上找，看哪个包的版本不一样，于是找到了 `brilliant-errors` 这个包，这个包的版本从 0.6.0 升级到了 0.6.1，再往上就没有变化了。

于是，我尝试手动安装 brilliant-errors 的 0.6.0 版本的到项目中，然后再次打包，果然构建成功。

这时候其实已经可以解决问题了，但是因为不是项目需要的包，安装这个包会给人造成歧义。于是思考有没有更好的方案。

在网上和微信群寻求帮助，有群友提到了 `resolutions` 关键字。

然后我就去搜了一下是什么东西。

### resolutions

`package.json` 文件里的 `resolutions` 字段用于解析选择性版本，当我们通过 yarn 安装的依赖的时候，会安装`resolutions` 中依赖指定的版本。

> 参考文档：[https://chore-update--yarnpkg.netlify.app/zh-Hans/docs/selective-version-resolutions](https://chore-update--yarnpkg.netlify.app/zh-Hans/docs/selective-version-resolutions)

这个正好可以解决我遇到的这个问题。于是我去搜了一下 npm 可不可以使用`resolutions` ，然后找到了这篇文章：[https://blog.csdn.net/cxwtsh123/article/details/123134278](https://blog.csdn.net/cxwtsh123/article/details/123134278)

需要安装一个[npm-force-resolutions](https://www.npmjs.com/package/npm-force-resolutions) 依赖，然后配置 scripts：

```json
"scripts": {
  "preinstall": "npx npm-force-resolutions"
}
```

然后就可以已正常 npm install 的方式来安装了。

但是经过尝试，发现不起作用，现在也没找到原因。

### overrides

继续找方案，偶然发现 npm 有个[overrides](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides)属性，可以实现类似`resolutions` 的功能，写法也是一样的：

```json
"overrides": {
  "brilliant-errors": "0.6.0"
}
```

试了一下，完美解决问题。

但是需要注意的是，`overrides`需要[npm≥v8.3](https://github.com/npm/cli/releases/tag/v8.3.0) 才支持。

为了让测试在配置好 node 的时候，就配置好 npm，我们选择了配置套的 node，而不需要单独安装 npm 的版本。

经过对比，最终选择的 node 版本如下：

```
E:\whty-code\BlueLink>npm -v
8.19.2

E:\whty-code\BlueLink>node -v
v16.18.1
```

![](/image/2023/2.png)

> PS：我之前还在 brilliant-errors 仓库提了[issue](https://github.com/inocan-group/brilliant-errors/issues/1)，现在看[0.7.1](https://github.com/inocan-group/brilliant-errors/issues/1#issuecomment-1338359976)版本已经修复了。

（完）
