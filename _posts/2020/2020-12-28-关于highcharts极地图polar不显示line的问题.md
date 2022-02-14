---
title: 关于highcharts极地图polar不显示line的问题
tags: highcharts
---

1. 目录
{:toc}


最开始就有这个问题，但是一直没有管他。这次因为概况页面UI改版，所以开始着手处理。

最开始以为是参数设置的问题，于是就找到了官方示例代码，把官方的一些参数加入到之前的代码里面发现不起作用。

于是就把整个官方代码原封不动拷贝到项目中进行尝试，然后发现还是没有显示极地图中间的线条。

<!--more-->

然后我怀疑是版本的问题，看了一下项目中highcharts使用的版本是v7.1.2，而官方实例是用的最新版的v8.2.2，于是我把官方的版本下载下来放到项目中使用，果然新版的线条出来了。

但是我想，之前的版本也不可能不能显示line啊？应该是有什么参数没设置正确。

然后我就在网上搜索这个版本polar不显示线的问题，然后找到这篇讨论：[https://www.highcharts.com/forum/viewtopic.php?t=42099](https://www.highcharts.com/forum/viewtopic.php?t=42099)

大意就是有个开发者也遇到相同的问题，当时的最新版就是v7.1.2也不显示line，不过作者没有复现，但是作者强调：

```
Please confirm that every script which you have attached is from one version, to avoid collision like in this case.
```

我想到我们项目除了使用到highcharts外还是用到了 highcharts-more这个js文件，是highcharts的扩展文件，是不是这两个js文件版本不匹配呢？

我看了下项目的 highcharts-more 版本是v6.0.1确实和v7.1.2差了不少。

于是我在官网下载了highcharts-more v7.1.2版本的，导入项目后，line终于出来了。

仅此为记。


PS：看看有line的样子：

![image](https://user-images.githubusercontent.com/23518990/103338367-2419ae00-4ab9-11eb-92d4-2c6ecf101eca.png)



（完）

