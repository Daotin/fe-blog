---
layout: mypost
title: 移动端浏览器对于 window.close 无效
tags:
  - JavaScript
---

下面的代码为什么在 PC 端 chrome 浏览器生效，但是在移动端不生效？

```js
// 关闭当前标签页
function closeCurrentPage() {
  window.opener = null;
  window.open("", "_self");
  window.close();
}

```

这是因为移动端浏览器对于 window.close() 方法的实现有所不同。在桌面端浏览器中，此方法通常用于关闭通过 JavaScript 打开的窗口。然而，在移动端浏览器中，window.close() 方法可能不会起作用，因为移动端浏览器通常不支持多窗口或标签页。

另外，出于安全原因，许多浏览器限制了 window.close() 方法的使用。在某些情况下，只有通过 JavaScript 打开的窗口才允许使用 window.close() 方法关闭。这意味着如果用户直接访问了页面，而不是通过 JavaScript 打开的，那么 window.close() 方法可能不会生效。

因此，在移动端浏览器中，这段代码可能不会达到预期的效果。要实现类似的功能，可以考虑使用其他方法，例如导航到其他页面，或者显示一个提示用户关闭标签页的消息。
