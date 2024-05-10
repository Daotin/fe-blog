---
layout: mypost
title: 当使用ref来渲染Echats的时候，tooltip不显示
tags:
  - echarts
---

当使用ref来渲染Echats的时候，tooltip不显示

https://blog.csdn.net/weixin_45304198/article/details/122115316

示例：

```
let myChart = ref()

myChart.value.setOption(option)
```

结果：不显示tooltip❌

```
let myChart

myChart.setOption(option)
```

结果：显示tooltip✅

追加：shallowRef应该也可以。