---
title: 一文搞懂JavaScript中各种宽高位置（全）
tags: JavaScript
---

1. 目录
{:toc}

> 阅读本文大概需要 12 分钟。

## 问题由来


刚开始学 DOM 操作中对于元素距离元素的距离问题总是迷迷糊糊的，虽然有万能的 `getCurrentStyle` 方式来取得所需要的属性，但是有时看别人的代码的时候，总会遇到很多简写的方式，或者有的时候为了简洁，关键字的方式更加合适，但是要求我们对这些属性的区别要特别清楚。

比如下面要说的 offset 系列，scroll 系列，client系列的距离，还有事件发生时 offsetX，clientX，pageX 等等的一些距离的总结，可以在我们忘记的时候翻翻一翻这篇文章，然后花最短的时间搞清楚它们之间的区别。

<!--more-->

## 元素视图位置属性

先看下示例代码：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        textarea {
            width: 200px;
            height: 200px;
            border: 6px solid red;
            padding: 5px;
            margin: 10px;
            /* overflow: scroll; */ /*加不加滚动条*/
            white-space: nowrap;    /*强制不换行*/
        }
    </style>
</head>

<body>
    <textarea>1</textarea>
</body>
<script>
    var textarea = document.querySelector("textarea");

    console.log(textarea.clientWidth, textarea.scrollWidth, textarea.offsetWidth);
    console.log(textarea.clientLeft, textarea.scrollLeft, textarea.offsetLeft);
</script>

</html>

```

  

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8b934ebfd8d48d381fef6b7ee941063~tplv-k3u1fbpfcp-zoom-1.image)

> - `clientWidth` = 200(可见区域) + 5(padding) + 5(padding)
> - `scrollWidth` = 200(可见区域) + 5(padding) + 5(padding)
> - `offsetWidth` = 200(可见区域) + 5(padding) + 5(padding) + 6(border) + 6(border)
> - `clientLeft` = 6(左边框宽度border-left)
> - `scrollLeft` = 0(横向滚动条滚动的距离)
> - `offsetLeft` = 10(元素左外border距离父元素左内border的距离)

当我把滚动条加上的时候：

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5afb815136b248eab0a3d0e56d466983~tplv-k3u1fbpfcp-zoom-1.image)

> - `clientWidth` = 200(可见区域) + 5(padding) + 5(padding) - 17(滚动条宽度)
> - `scrollWidth` = 200(可见区域) + 5(padding) + 5(padding) - 17(滚动条宽度)
> - `offsetWidth` = 200(可见区域) + 5(padding) + 5(padding) + 6(border) + 6(border)
> - `clientLeft` = 6(左边框宽度border-left)
> - `scrollLeft` = 0(横向滚动条滚动的距离)
> - `offsetLeft` = 10(元素左外border距离父元素左内border的距离)

当文字过长滚动条可以滑动的时候：

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b6c549b9299c4249abfb17e83654ac67~tplv-k3u1fbpfcp-zoom-1.image)

> - `clientWidth` = 200(可见区域) + 5(padding) + 5(padding) - 17(滚动条宽度)
> - `scrollWidth` = 280(整个内容，包括不可见区域) + 5(padding) + 5(padding) - 17(滚动条宽度)
> - `offsetWidth` = 200(可见区域) + 5(padding) + 5(padding) + 6(border) + 6(border)
> - `clientLeft` = 6(左边框宽度border-left)
> - `scrollLeft` = 0(横向滚动条滚动的距离)
> - `offsetLeft` = 10(元素左外border距离父元素左内border的距离)

由于每次打开时，滚动条的位置不变，所以我需要 js 设置滚动滚动条的时候，看每个值的变化：

```js
textarea.onscroll = function () {
    console.log(textarea.clientWidth, textarea.scrollWidth, textarea.offsetWidth);
    console.log(textarea.clientLeft, textarea.scrollLeft, textarea.offsetLeft);
};

```

  

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75e9928ea06f49da95b20c5073ffde11~tplv-k3u1fbpfcp-zoom-1.image)

我们可以发现，只有 `scrollLeft` 是在改变的。

上面是 width 系列 和 left 系列的一些值的情况，那么相应的 height 系列和 top 系列的值也是一样的。

![image](https://user-images.githubusercontent.com/23518990/128631590-15221d90-595c-4021-b2a6-6c21075263d0.png)
![image](https://user-images.githubusercontent.com/23518990/128631598-b70002fc-7d89-4a34-8753-53c5508982cf.png)
![image](https://user-images.githubusercontent.com/23518990/128631623-16edde92-318a-47d9-b815-04540312fe70.png)

### client系列

> - **clientWidth = 可视区域宽度 （不包含border和滚动条）**
> - **clientHeight = 可视区域高度（不包含border和滚动条）**
> - **clientLeft：相当于元素左border(border-left)的宽度**
> - **clientTop：相当于元素上border(border-top)的宽度**

### scroll系列

> - **scrollWidth = 内容实际宽度，包括因为滚动而被隐藏的部分（在没有滚动条的情况下，元素内容的总宽度）**
> - **scrollHeight = 内容实际高度，包括因为滚动而被隐藏的部分（在没有滚动条的情况下，元素内容的总高度）**
> - **scrollLeft：横向滚动条滚动的距离**
> - **scrollTop：纵向滚动条滚动的距离**

> 注意：
> 
> clientHeight和scrollHeight在没有滚动条的时候计算的高度是一样的，如果有滚动条scrollHeight和clientHeight就不会相等了，如果滚动条滚到底那么`scrollHeight = clientHeight + scrollTop`。



### offset系列

在此之前，我们先看看一个属性：`offsetParent`。

offset是偏移的意思，既然是偏移就要有一个参照物，这个参照物就是 offsetParent。它指的是距离当前元素最近的定位父元素（`position != static`），这个定位父元素就是我们计算所有offset属性的参照物。

距离如下：

> - **offsetWidth = 可视区域宽度 （包含border和滚动条）**
> - **offsetHeight = 可视区域高度 （包含border和滚动条）**
> - **offsetLeft：元素相对最近的祖先定位元素的左偏移值**
> - **offsetTop：元素相对最近的祖先定位元素的上偏移值**





## windows浏览器窗口宽高



### 浏览器窗口宽高

| 属性名               | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| `window.innerHeight` | 浏览器窗口高度，**如果存在水平滚动条，则包括滚动条**         |
| `window.innerWidth`  | 浏览器窗口宽度，**如果存在垂直滚动条，则包括滚动条**         |
| `window.outerWidth`  | 浏览器窗口整个宽度，包括侧边栏，窗口镶边和调正窗口大小的边框 |
| `window.outerHeight` | 浏览器窗口整个高度，包括窗口标题、工具栏、状态栏等           |

如果没有滚动条，则下面代码宽高相同：

```js
let width = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth
let height = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight
```

> 注意：IE8及以下版本不支持`window.innerHeight`和`window.innerWidth`等属性。
> 对于不支持window.innerHeight等属性的浏览器中，可以读取documentElement和body的高度。



**document.documentElement.clientHeight 和 document.body.clientHeight的区别**

- `document.documentElement.clientHeight`：不包括整个文档的滚动条，但包括`<html>`元素的边框。
- `document.body.clientHeight`：不包括整个文档的滚动条，也不包括`<html>`元素的边框，也不包括`<body>`的边框和滚动条。



### 获取整个页面滚动值

涉及到的属性：

- `window.pageXOffset`（等同`window.scrollX`）
- `window.pageYOffset`（等同`window.scrollY`）

| 属性名               | 描述                             | 备注                                              | 别名                           |
| -------------------- | -------------------------------- | ------------------------------------------------- | ------------------------------ |
| `window.pageXOffset` | 返回文档在水平方向滚动的像素值   | 只读属性，没有默认值，不支持的浏览器则是undefined | `pageXOffset`是`scrollX`的别名 |
| `window.pageYOffset` | 返回文档在垂直方向已滚动的像素值 | 只读属性，没有默认值，不支持的浏览器则是undefined | `pageYOffset`是`scrollY`的别名 |

```js
window.pageXOffset == window.scrollX; // 总是 true
window.pageYOffset == window.scrollY; // 总是 true
```



### 浏览器窗口在显示器中的位置

涉及到的属性：

- screenX
- screenY
- screenLeft
- screenTop

| 属性名              | 描述                           |
| ------------------- | ------------------------------ |
| `window.screenX`    | 浏览器窗口在显示器中的水平位置 |
| `window.screenY`    | 浏览器窗口在显示器中的垂直位置 |
| `window.screenLeft` | 浏览器窗口距离屏幕左边界的距离 |
| `window.screenTop`  | 浏览器窗口距离屏幕上边界的距离 |



###  显示器相关属性

| 属性名                      | 描述                                     |
| --------------------------- | ---------------------------------------- |
| `window.screen.height`      | 显示器屏幕的高度，包括工具栏、状态栏等。 |
| `window.screen.width`       | 显示器屏幕的宽度，包括工具栏、状态栏等。 |
| `window.screen.availHeight` | 浏览器窗口在屏幕上可占用的高度。         |
| `window.screen.availWidth`  | 浏览器窗口在屏幕上可占用的宽度。         |
| `window.screen.colorDepth`  | 表示显示器的颜色深度。                   |
| `window.screen.pixelDepth`  | 该属性基本上与colorDepth一样。           |





## 鼠标事件相关的坐标距离

鼠标事件中有很多描述鼠标事件发生时的坐标信息的，给大家介个图看看：

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87f0d1576eda4f88a6e11c996fc9a7e4~tplv-k3u1fbpfcp-zoom-1.image)

这么多的坐标位置到底有什么区别呢？下面两张图（来自网络）带你一眼看穿它们之间的区别：

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/75b62085f6e74c579639e04c7baf852d~tplv-k3u1fbpfcp-zoom-1.image)

![图片](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89daca8f462f4d9994ee581e77ddb6aa~tplv-k3u1fbpfcp-zoom-1.image)



> - **clientX，clientY** = 鼠标点击位置距离浏览器**可视区域**左边的距离
> - **offsetX，offsetY** = 鼠标点击位置距离元素左边的距离，不包括左border。
> - **pageX，pageY** = 鼠标点击位置相对于文档的坐标 scrollLeft + clientX （**但是IE8不支持**）
> - **layerX，layerY** = offsetX + 左border + 左边滚动条滚动的距离
> - **x，y** = 鼠标点击位置距离浏览器可视区域的左边距离（相当于 clientX）。
> - **screenX，screenY** = 鼠标点击位置距离电脑屏幕左边的距离。

同样，上面都是 X 系列的位置比较，Y的方向上也是一样的。

看完这些，你对 DOM 元素的距离相关的属性都了解了吗？

## getBoundingClientRect方法

返回值：

（ top, left, right, 和 bottom四个属性值，大小都是相对于文档视图左上角计算而来。）

> - left（当前元素左侧相对于可视区左上角的距离）
> - right（当前元素右侧相对于可视区左上角的距离）
> - top（当前元素上边相对于可视区左上角的距离）
> - bottom（当前元素下边相对于可视区左上角的距离）
> - width（元素自身可视宽度）
> - height（元素自身可视高度）

---

想看更多精彩内容，关注公众号「前端队长」，获取更多前端技术与个人成长相关内容。



（完）

