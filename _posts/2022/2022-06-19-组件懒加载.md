---
layout: mypost
title: 路由懒加载和组件懒加载区别
tags: vue
---


## 背景

一直都听到路由懒加载，还有组件懒加载，还有延迟加载，动态组件，异步组件啥的，今天来梳理一下。



## 正文

### 动态组件
很简单。就是下面我们常用的。

```vue
<component v-bind:is="currentTabComponent"></component>
```



### 异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。一个推荐的做法是将异步组件和 [webpack 的 code-splitting 功能](https://webpack.js.org/guides/code-splitting/)一起配合使用：

```vue
component：resolve=>(['./my-async-component'，resolve])
```

也可以返回一个 `Promise`，所以把 webpack 2 和 ES2015 语法加在一起，我们可以这样使用动态导入：

```
let component = () => import('./my-async-component')
```



### 路由懒加载

在单页应用中，如果没有应用懒加载，运用webpack打包后的文件将会异常的大，造成进入首页时，需要加载的内容过多，延时过长，不利于用户体验，路由懒加载可以解决白屏问题。

路由懒加载简单来说就是`延迟加载`或`按需加载`，即在需要的时候的时候进行加载。



**vue异步组件实现懒加载**

```js
import Vue from 'vue'
import Router from 'vue-router'
/* 此处省去之前导入的HelloWorld模块 */
Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: resolve=>(require(["@/components/HelloWorld"],resolve))
    }
  ]
})

```

**或者动态导入import**

```js
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: ()=>import("@/components/HelloWorld")
    }
  ]
})

```



### 组件懒加载（延迟加载组件）

```vue
<template>
  <div class="hello">
    <One-com></One-com>
  </div>
</template>

<script>
export default {
  components:{
    "One-com":resolve=>(['./one'],resolve)
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

```

**或者动态导入import**

```vue
<template>
  <div class="hello">
    <One-com></One-com>
  </div>
</template>

<script>
export default {
  components:{
    "One-com": ()=>import("./one");
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

```



## 总结

随着应用程序越来越大，如果不采用路由懒加载，当打包构建应用时，JavaScript 包会变得非常大，影响首屏加载速度。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会更加高效，这就是路由懒加载。

**路由懒加载**一般有两种方式，一种是使用Vue异步组件的方式，一种是使用ES6动态导入import 的方式，都可以达到目的。

而**组件懒加载（也就是延迟加载组件）**，是在我们加载完一个界面时，该组件中的一些子组件在最开始的时候不加载，而在使用的时候才加载。比如Tootip组件，可能用户在这个页面不需要工具提示的情况下浏览整个系统，所以把Tootip这种组件采用组件懒加载的方式可以进一步提升该页面的渲染速度。

简单来说，**路由懒加载是大粒度优化加载速度，组件懒加载是小粒度提升加载速度**。

**所以，优先使用路由懒加载，必要的使用组件懒加载。**

（完）

