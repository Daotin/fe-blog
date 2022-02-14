---
title: 使用vue-cli3+快速搭建简单的vue项目
tags: vue vue-cli
---

1. 目录
{:toc}

## 问题描述
使用`vue-cli3+`快速搭建一个可用作demo的项目。

<!--more-->

## 解决方案
步骤如下：

1. 全局安装vue指令包：`npm install -g @vue/cli@3`

安装之后，你就可以在命令行中访问 `vue` 命令。你可以通过简单运行 `vue --version`，看看是否打印出当前安装的vue-cli版本号，来验证它是否安装成功。

2. 使用 `vue create vue-demo` 创建一个新项目

这时候会进入命令行交互界面，选择第一项`vueConfig`配置项即可快速搭建简单的vue项目。

![1](https://user-images.githubusercontent.com/23518990/71805802-18f89180-30a2-11ea-9dd6-80fb9f82fd6f.png)


3. 此时通过`npm run serve` 即可启动vue项目。

### 项目配置自动引入全局less文件
我们在入库文件`main.js`引入`import './assets/css/common.less'` 是无效的，当组件在使用common.less下的变量的时候，会提示undefined。

vue官方文档给出的解决方案是配置`vue.config.js`文件使得每个vue文件自动引入全局less文件。

步骤如下：
1. 项目安装`less`和`less-loader`

```
npm i less less-loader -D
```

2. 给你的项目添加插件`vue add style-resources-loader`

运行后会在命令行让你选择需要预处理的语言：有less，sass，scss等，这里我们选择less。也就是我们之后再vue.config.js中 `preProcessor` 对应的值。


3. 在项目src同级目录新建`vue.config.js`文件，该文件会会被 `@vue/cli-service` 自动加载。vue.config.js 文件内容如下：

`preProcessor`: 为你添加插件时选择的语言。
`patterns`: 全局less文件路径。

```js
module.exports = {
    pluginOptions: {
        'style-resources-loader': {
            preProcessor: 'less',
            patterns: ['./src/assets/css/*.less'],
        }
    }
};
```
然后就可以在各个vue文件中使用less中定义的变量了。


### vue.config.js的其他配置

```js
// const path = require('path');
// const vConsolePlugin = require('vconsole-webpack-plugin'); // 引入 移动端模拟开发者工具 插件 （另：https://github.com/liriliri/eruda）
// const CompressionPlugin = require('compression-webpack-plugin'); //Gzip
// const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin; //Webpack包文件分析器
// const baseUrl = process.env.NODE_ENV === "production" ? "/static/" : "/"; //font scss资源路径 不同环境切换控制

module.exports = {
    //基本路径
    //baseUrl: './',//vue-cli3.3以下版本使用
    publicPath:'./',//vue-cli3.3+新版本使用
    //输出文件目录
    outputDir: 'dist',
    // eslint-loader 是否在保存的时候检查
    lintOnSave: true,
    //放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录。
    assetsDir: '',
    //以多页模式构建应用程序。
    pages: undefined,
    //是否使用包含运行时编译器的 Vue 构建版本
    runtimeCompiler: false,
    //是否为 Babel 或 TypeScript 使用 thread-loader。该选项在系统的 CPU 有多于一个内核时自动启用，仅作用于生产构建，在适当的时候开启几个子进程去并发的执行压缩
    parallel: require('os').cpus().length > 1,
    //生产环境是否生成 sourceMap 文件，一般情况不建议打开
    productionSourceMap: false,
    devServer: {
        host: 'localhost',
        // host: "0.0.0.0",
        port: 8000, // 端口号
        https: false, // https:{type:Boolean}
        open: false, //配置自动启动浏览器  http://172.11.11.22:8888/rest/XX/
        hotOnly: true, // 热更新
        // proxy: 'http://localhost:8000'   // 配置跨域处理,只有一个代理
        proxy: { //配置自动启动浏览器
            "/XX/*": {
                target: "http://172.11.11.11:7071",
                changeOrigin: true,
                // ws: true,//websocket支持
                secure: false
            },
            "/XX2/*": {
                target: "http://172.12.12.12:2018",
                changeOrigin: true,
                //ws: true,//websocket支持
                secure: false
            },
        }
    },

    // 第三方插件配置 https://www.npmjs.com/package/vue-cli-plugin-style-resources-loader
    pluginOptions: {
        'style-resources-loader': {
            preProcessor: 'less',
            // patterns: ['D:\\code\\vue-cli3-demo\\src\\assets\\css\\*.less',],
            patterns: ['./src/assets/css/*.less'],
        }
    }
};

```





## 参考链接

- https://cli.vuejs.org/zh/guide/
- https://cli.vuejs.org/zh/guide/css.html#%E9%A2%84%E5%A4%84%E7%90%86%E5%99%A8






（完）

