---
title: Vue路由配置history模式
tags: Vue
---

1. 目录
{:toc}


## vue需要node.js吗？

你可以用 script 标签的形式引入vue.min.js 这样的，不需要nodejs。

使用node有几件事，打包部署，解析vue单文件组件，解析每个vue模块，拼在一起，转码es6，less等，启动测试服务器 localhost:8080, 帮你管理 vue-router等插件。

所以每次当我们使用 `npm run dev` 的时候，页面会打开一个 `localhost:3000` 的页面，这其实就是node为我们启动了一个Node.js 静态文件服务器。

<!--more-->


## hash vs history
`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，**页面不会向后端发出请求。**

当你使用 history 模式时，URL 就像正常的 url，例如 http://yoursite.com/user/id ，也好看！

不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 http://oursite.com/user/id 就会返回 404，这就不好看了。

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。

前面不是说了，我们vue启动了Nodejs静态文件服务器了吗？为啥还不能直接使用history？

如果你在 history 模式下使用 Vue Router，**是无法搭配简单的静态文件服务器的（也就是说你需要配置一下就可以借助这个Nodejs使用history模式了，默认是不提供的）**。

例如，如果你使用 Vue Router 为 /todos/42/ 定义了一个路由，开发服务器也已经配置了相应的 localhost:3000/todos/42 响应，但是一个为生产环境构建架设的简单的静态服务器会却会返回 404。

为了解决这个问题，你需要配置生产环境服务器，将任何没有匹配到静态文件的请求回退到 index.html。



## webpack配置history
### 方式1：通过命令行的方式
形如 `webpack-dev-server --history-api-fallback`,不过一般都是放到 `scripts`节点下，如：

```json
// package.json
 "scripts": {
    "dev": "webpack-dev-server  --env.dev --history-api-fallback"
  }
```



### 方式2 在 webpack 配置文件的devServer配置


```javascript
// webpack.config.js
devServer:{
  ...
 historyApiFallback: true
}

```


（完）


