---
title: 那些鲜为人知的CSS属性
tags: css
---

1. 目录
{:toc}

<!--more-->


## attr

 CSS表达式 attr() 用来获取选择到的元素的某一HTML属性值，并用于其样式。它也可以用于伪元素，属性值采用伪元素所依附的元素。 

```html
<p data-unit="°C">当前温度为30</p>

<style>
    [data-unit]:after {
        content: attr(data-unit);
        color: red;
    }
</style>
```

打印内容为：当前温度为30°C



##  currentColor 

 currentColor不是一个css属性，而是color的属性值。它返回当前的标签所继承的文字颜色。 

```css
div {
    color: red;
    border-color: currentColor;  // red
}
```





##  will-change 

属性 `will-change` 为web开发者提供了一种告知浏览器该元素会有哪些变化的方法，这样浏览器可以在元素属性真正发生变化之前提前做好对应的优化准备工作。 这种优化可以将一部分复杂的计算工作提前准备好，使页面的反应更为快速灵敏。 

当我们通过某些行为（点击、移动或滚动）触发页面进行大面积绘制的时候，浏览器往往是没有准备的，只能被动使用CPU去计算与重绘，由于没有事先准备，应付渲染够呛，于是掉帧，于是卡顿。而`will-change`则是真正的行为触发之前告诉浏览器：“浏览器同学，我待会儿就要变形了，你心理和生理上都准备准备”。于是乎，浏览器同学把GPU给拉上了，从容应对即将到来的变形。

> 该属性使用需谨慎。



使用方法：

- https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change 

- https://www.zhangxinxu.com/wordpress/2015/11/css3-will-change-improve-paint/ 



（完）


