---
title: 文字背景对比度contrast ratio的计算公式
tags: JavaScript CSS
---

1. 目录
{:toc}

## 对比度标准
MD规范里说：**文本应该保持至少 4.5:1 （基于亮度值计算）的对比度以保持文本清晰；最佳对比度为 7:1。**

对比度的计算规则我们可以简单的理解为两个颜色的相对亮度相除得到的值，比如：两个白色的对比度是 1:1 , 白色（#FFFFFF）与黑色（#000000）的对比度为 21:1，也就是说对比度的范围在 1:1 与 21:1 之间。

<!--more-->

> **为什么基于亮度计算？**
> 
> W3C (万维网联盟)中提到，对没有颜色缺陷的人进行阅读性能评估，发现色调和饱和度对易读性的影响很小或者是没有影响，而亮度对比度对易读性的影响很大。所以，颜色差别不是关键，即使对色弱的人，只要有足够的亮度对比度就不会影响阅读。


## 对比度等级

WCAG 2.0 中将颜色对比等级分为3种，A级，AA级，AAA级，等级越高意味着颜色的对比度越高，呈现出来的视觉压力越大：

- `A 级` （采用3:1的对比度，是普通观察者可接受的最低对比度）
- `AA 级`（采用4.5:1 的对比度，是普通视力损失的人可接受的最低对比度）
- `AAA 级`（采用7:1的对比度，是严重视力损失的人可接受的最低对比度）

![image](https://user-images.githubusercontent.com/23518990/90106067-76ac3500-dd79-11ea-866d-58bfa6b429b0.png)



## 计算两个颜色的对比度

```javascript
luminanace(r, g, b) {
    var a = [r, g, b].map(function (v) {
        v /= 255;
        return v <= 0.03928
            ? v / 12.92
            : Math.pow((v + 0.055) / 1.055, 2.4);
    });
    return a[0] * 0.2126 + a[1] * 0.7152 + a[2] * 0.0722;
},
contrast(rgb1, rgb2) {
    // hex to rgb
    var result1 = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(rgb1);
    var result2 = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(rgb2);
    var rgb1Obj = result1 ? {
        r: parseInt(result1[1], 16),
        g: parseInt(result1[2], 16),
        b: parseInt(result1[3], 16)
    } : null;
    var rgb2Obj = result2 ? {
        r: parseInt(result2[1], 16),
        g: parseInt(result2[2], 16),
        b: parseInt(result2[3], 16)
    } : null;

    var lum1 = this.luminanace(rgb1Obj.r, rgb1Obj.g, rgb1Obj.b);
    var lum2 = this.luminanace(rgb2Obj.r, rgb2Obj.g, rgb2Obj.b);
    
    // 亮色 / 暗色
    var brightest = Math.max(lum1, lum2);
    var darkest = Math.min(lum1, lum2);
    
    return (brightest + 0.05) / (darkest + 0.05);
}

console.log('contrast>>>>>>',this.contrast('#ffffff', '#ffff00') ); // 1.0738392309265699
```


## 参考文档
- https://www.zhangxinxu.com/wordpress/2018/11/css-background-color-font-auto-match/
- http://www.xueui.cn/experience/app-experience/selection-of-contrast-between-text-and-background.html
- https://stackoverflow.com/questions/9733288/how-to-programmatically-calculate-the-contrast-ratio-between-two-colors
