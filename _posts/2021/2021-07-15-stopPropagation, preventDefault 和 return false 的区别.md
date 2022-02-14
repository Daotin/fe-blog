---
title: stopPropagation, preventDefault 和 return false 的区别
tags: JavaScript
---

1. 目录
{:toc}

大家好，我是前端队长Daotin，想要获取更多前端精彩内容，关注我(全网同名)，解锁前端成长新姿势。

以下正文：

<!--more-->

## **stopPropagation**

**阻止事件的冒泡和捕获。**

因为事件可以在各层级的节点中传递, 不管是冒泡还是捕获, 有时我们希望事件在特定节点执行完之后不再传递, 可以使用事件对象的 `stopPropagation()` 方法。

## preventDefault

**阻止浏览器默认行为。**

浏览器的默认行为：对于一些特定的事件，浏览器有它默认的行为。

例如：

- 点击链接会进行跳转

- 点击鼠标右键会呼出浏览器右键菜单

- 填写表单时按回车会自动提交到服务器等



## return false;

当你调用它时会做 3 件事：

- event.preventDefault() – 它停止浏览器的默认行为。

- event.stopPropagation() – 它阻止事件传播（或“冒泡”）DOM。

- 停止回调执行并立即返回。



例如可以通过 `return false` 来阻止 `a` 标签的默认跳转事件，当然也可以用 `preventDefault()` 来代替。



**表格对比：**

|方法|事件冒泡/捕获|默认事件|
|-|-|-|
|stopPropogation()|不冒泡|执行|
|preventDefault()|冒泡|不执行|
|return false|不冒泡|不执行|




> **补充：stopPropagation和stopImmediatePropagation区别**

- 共同点 都可以阻止事件冒泡，父节点无法接受到事件。

- 不同点 `stopPropagation`**不会影响**该元素后续相同事件的执行，而`stopImmediatePropagation`会阻止该元素后续相同事件的执行。

示例：

```HTML
<div>
  <p>Daotin</p>
</div>
```

给p绑定一个click事件，再给p绑定第二个click事件  ，给div绑定一个click事件。

如果在p的第一个click事件回调中`stopPropagation`，div的click事件回调不执行，但是p的第二个click事件回调会执行，

如果在p的第一个click事件回调中`stopImmediatePropagation`，div的click事件回调不执行，而且p的第二个click事件也不执行。

&nbsp;

--End--

![](https://gitee.com/daotin/img/raw/master/gzh.png)
