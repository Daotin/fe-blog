---
layout: mypost
title: keep-alive的应用
tags:
  - Vue
---

今天，项目经理找到我，让我把一个项目的页面加上缓存，也就是搜索条件啥的，当切换菜单的时候，要保留搜索条件。

我一看，这不就是keep-alive的功能吗，系统框架竟然没有做，那正好，我来做一下。

keep-alive的基本概念：

官方文档：https://v2.cn.vuejs.org/v2/api/#keep-alive

大概就两个重点：

1、`<keep-alive>` 包裹动态组件时，会缓存组件。

2、如果使用include，那么需要组件的`name`属性与之对应。

---

跟项目精力沟通，有三种方式：

1、全部页面缓存

2、前端配置，可选哪些页面缓存

3、系统配置，可以在菜单页面配置哪些页面缓存

方式1，最简单，直接套一个keep-alive就好了，只要保证每个路由的name不相同即可。

```html
<keep-alive>
    <router-view :key="$route.name"></router-view>
</keep-alive>
```

方式2，可能需要在路由中配置一个meta属性：

```js
 {
    path: '/route1',
    name: 'Route1',
    component: () => import('@/components/MyComponent.vue'),
    meta: { keepAlive: true }
  },
```

然后增加include，只有keepAlive=true的才加入到include列表中去：

```html
<keep-alive :inlcude="['aaa', 'bbb']">
    <router-view :key="$route.name"></router-view>
</keep-alive>
```

方式3是最麻烦的，需要在菜单页面配置每个页面的name，然后后端配合，通过接口获取到所有的name，加入到include列表中。

这就是三种keep-alive的使用，我写完了。