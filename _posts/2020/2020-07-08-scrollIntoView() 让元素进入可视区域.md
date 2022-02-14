---
title: scrollIntoView() 让元素进入可视区域
tags: JavaScript
---

1. 目录
{:toc}

## 介绍

DOM元素的`scrollIntoView()`方法是一个IE6浏览器也支持的原生JS API，可以让元素进入视区，通过触发滚动容器的定位实现。

<!--more-->

## 语法
```js
element.scrollIntoView(); // 等同于element.scrollIntoView(true) 
element.scrollIntoView(boolean); // Boolean型参数,true or false
element.scrollIntoView(options); // Object型参数
```
当参数为Boolean时：
- 如果为`true`，元素的顶端将和其所在滚动区的可视区域的顶端对齐。相应的 options:     `{block: "start", inline: "nearest"}`。
- 如果为`false`，元素的底端将和其所在滚动区的可视区域的底端对齐。相应的options:     `{block: "end", inline: "nearest"}`。

当参数为options对象时，属性有：
- `behavior`： 定义动画过渡效果， "auto"或 "smooth（平滑滚动）" 之一。默认为 "auto"。
- `block`：定义垂直方向的对齐， "start", "center", "end", 或 "nearest"之一。默认为 "start"。
- `inline `：定义水平方向的对齐， "start", "center", "end", 或 "nearest"之一。默认为 "nearest"。

> 彩蛋:
> CSS平滑滚动方式：
> ```css
> .box {
>     scroll-behavior: smooth; 
> }
> ```


## 参考链接
https://www.zhangxinxu.com/wordpress/2018/10/scroll-behavior-scrollintoview-%E5%B9%B3%E6%BB%91%E6%BB%9A%E5%8A%A8/

（完）
