---
layout: mypost
title: 关于全局守卫的return和next区别
tags:
  - vue-router
---

在vue-router4以前，一直使用next的，但是也经常容易出错。

在vue-router4以后，更推荐使用return。

## next的用法

- **`next()`**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
- **`next(false)`**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
- **`next('/')` 或者 `next({ path: '/' })`**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [router-link 的 to prop](https://v3.router.vuejs.org/zh/api/#to) 或 [router.push](https://v3.router.vuejs.org/zh/api/#router-push) 中的选项。
- **`next(error)`**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 **`[router.onError()](https://v3.router.vuejs.org/zh/api/#router-onerror)`** 注册过的回调。

## return的用法：

- `false`: 取消当前的导航。如果浏览器的 URL 改变了(可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
- 一个[路由地址](https://router.vuejs.org/zh/api/#routelocationraw): 通过一个路由地址跳转到一个不同的地址，就像你调用 **[router.push()](https://router.vuejs.org/zh/api/#push)** 一样，你可以设置诸如 `replace: true` 或 `name: 'home'` 之类的配置。当前的导航被中断，然后进行一个新的导航，就和 `from` 一样。
    - `return { name: 'Login' }`
    - `return '/login'`
- 如果什么都没有，`undefined` 或返回 `true`，**则导航是有效的**，并调用下一个导航守卫

**return和next对比：**

next的缺点是，其后面的内容会继续执行。

而return则不会。

