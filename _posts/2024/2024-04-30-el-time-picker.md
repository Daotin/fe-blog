---
layout: mypost
title: el-time-picker无法选择跨天的时间范围
tags:
  - ElementUI
---

有一个需求，设置勿扰时间为开始时间为22:00，结束时间为06:00，

当el-time-picker设置了`is-range`的属性的时候，无法设置跨天的时间，默认必须开始时间要小于结束时间。因为它选择的是一个时间范围。

比如下面设置会导致小时和分钟无法选择：

```
defaultTime: [new Date(2000, 1, 1, 22, 0, 0), new Date(2000, 1, 2, 6, 0, 0)]
```

解决办法：

分成两个el-time-picker进行设置，互不关联。

```
dndModeStart: new Date(2000, 1, 1, 22, 0, 0), // 勿扰模式开始时间
dndModeEnd: new Date(2000, 1, 1, 6, 0, 0), // 勿扰模式结束时间
```


