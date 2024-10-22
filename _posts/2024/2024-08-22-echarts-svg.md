---
layout: mypost
title: echarts缩放后图标模糊问题
tags: [echarts]
---

> 将默认的`canvas`渲染改成`svg`渲染即可。
> {: .prompt-tip }

代码：

```js
// 引入
import 'zrender/lib/svg/svg';

this.echartsInstance = echarts.init(this.$refs.chartRef, null, { renderer: 'svg' });

// 还可以设置像素比为2  { devicePixelRatio: 2 }， 默认取浏览器的值window.devicePixelRatio，不过貌似效果不大
this.echartsInstance = echarts.init(this.$refs.chartRef, null, { renderer: 'svg', devicePixelRatio: 2 });
```

参考文档：

- https://echarts.apache.org/v4/zh/api.html#echarts.init
- https://echarts.apache.org/v4/zh/tutorial.html#%E4%BD%BF%E7%94%A8%20Canvas%20%E6%88%96%E8%80%85%20SVG%20%E6%B8%B2%E6%9F%93
