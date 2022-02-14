---
title: 如何用css画一个文件上传图案？
tags: css
---

1. 目录
{:toc}

如下图，如果是你，你会怎么实现下面图案？

![微信截图_20191213170143](https://user-images.githubusercontent.com/23518990/70788006-e624eb80-1dca-11ea-97ad-f68a4b3d92e9.png)


通常我们会通过字体图标来显示中间的加号，外层用一个div包裹即可；或者使用伪元素来模拟中间的一横一竖，这都比较麻烦。

其实我们可以直接使用css的`outline`属性就可以实现。

<!--more-->

## 轮廓属性outline

`outline`属性是用来设置一个或多个单独的轮廓属性的简写属性 ， 例如 outline-style, outline-width 和 outline-color。  

轮廓有下面几个属性：

```css
{
    outline-style: solid;
    outline-width: 10px;
    outline-color: red;
}
```

- `outline-width`：元素的轮廓的厚度，取值用`xxxpx`，或者`thin`,'medium','thick'。
- `outline-style`：元素的轮廓的样式，取值除了有常见的`solid`,`dotted`,`dashed`还有`double`,`groove`等（https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-style）
- `outline-color`：元素的轮廓的颜色。

他们有一种简写形式：

```css
{
    outline: 10px solid red;
}
```



## 轮廓的特点

**轮廓不占据空间，它们被描绘于内容之上。**

可以做到下图的效果：

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190813101002.png)


我发现，当设置 `outline-offset` 为负值的时候，轮廓会出现在div的内部，如果继续扩大其负值，最终轮廓会收缩成一个“➕”加号，正好可以作为文件上传样式中间的加号。

于是代码就来了：

```css
div {
    margin: 100px;
    width: 100px;
    height: 100px;
    outline: 15px solid #545454;
    outline-offset: -66px;
    border: 2px solid #545454;
}
```

`outline-offset: -66px;` 是关键，它表示**轮廓距div边**的距离，如果为负值则会往里面收缩，最后形成一个加号。具体上传样式的大小和outline的宽度都需要自己慢慢调整已达到大和谐。



> 需要注意的是：
>
> - 容器得是个正方形
> - outline 边框本身的宽度不能太小

## border 和 outline 区别
border 和 outline 很类似，但有如下区别：

- outline不占据空间，绘制于元素内容周围。
- 根据规范，outline通常是矩形，但也可以是非矩形的。

## 注意项

将 outline 设置为 0 或 none 会移除浏览器的默认聚焦样式。








（完）


