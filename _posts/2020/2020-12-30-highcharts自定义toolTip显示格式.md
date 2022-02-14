---
title: highcharts自定义toolTip显示格式，显示传入series.data之外的数据
tags: highcharts
---

1. 目录
{:toc}

假如有如下场景，series.data传入的是处理后百分比数据：

<!--more-->

```js
// 维度1：得分1，总分2
// 维度2：得分4，总分10
// 维度1：得分6，总分10

Highcharts.chart('container', {
    title: {
        text: 'Demo'
    },
    xAxis: {
        categories: ['维度1', '维度2', '维度3'],
    },
    yAxis: {
      min: 0,
      max: 100,
    },
    tooltip: {
        pointFormat: '<span style="color:{series.color}">{series.name}: <b>{point.y:,.0f}%</b><br/>',
        shared: true
    },

    series: [{
        name: '维度分类',
        data: [50,40,60]
    }]
});
```
![image](https://user-images.githubusercontent.com/23518990/103338512-9a1e1500-4ab9-11eb-8e86-d77ec6b77c27.png)

这样每个点的tip显示都是传入的data的百分比数据。

现在有个需求，我想让点的tip显示的是分数，而不是百分比，怎么办呢？

由于传入的是series.data之外的数据，所以我们不能使用`pointFormat`属性或者`pointFormatter`方法，因为这两个都是对传入的data数据进行变形处理。

而`formatter`方法可以解决我们问题：

思路如下：**formatter方法里面我们可以拿到当前point点的值和其在data数组的索引index，然后把维度分组成数组，该数组的index位置就是当前point值的维度分。**

局部代码如下：
```js
tooltip: {
    shared: true,
    // pointFormat: '<span style="color:{series.color}">{series.name}: <b>{point.y:,.0f}%</b><br/>',
    formatter: function () {
        return this.points.reduce(function (s, point) {
            return s + '<br/>' +
                `<span style="color:${point.series.color}">${point.series.name}</span>` + ': ' + me.catescore[point.point.index] + '分';
        }, '<b>' + this.x + '</b>');
    },
},
```

![image](https://user-images.githubusercontent.com/23518990/103338539-abffb800-4ab9-11eb-8aa0-1988b5fa7cbb.png)

可能有人疑问，为什么不直接给series.data传入维度的分数而传入百分比？

因为维度的总分参差不齐，我想都把维度都转化成百分制的形式（yAxis设置了min和max），这样整体显示美观一些。

参考链接：
- [https://api.highcharts.com/highcharts/tooltip.formatter](https://api.highcharts.com/highcharts/tooltip.formatter)


（完）

