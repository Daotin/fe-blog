---
layout: mypost
title: elementUI InfiniteScroll 无限滚动偶尔不触发问题
tags: elementUI
---

1. 目录
{:toc}

<!--more-->

## 背景

客户反馈框出来的对话框无法下拉加载更多，只能显示十来个对话记录；谷歌，qq和edge多个浏览器均无法加载；

但是运营，测试和本机均无法复现。

远程连接上客户的电脑后，确实存在无法滚动的问题，临时方法：

目前让用户在滚不动的时候先缩放到90% （Ctrl+滚轮下），此时可以滚动进行查看。

![](/image/4.png)

## 问题分析

由于本机正常无法复现，通过缩放到110%可以复现。但是具体原因还是未知。



## 解决办法

InfiniteScroll有一个属性`infinite-scroll-distance` 表示触发加载的距离阈值，单位px，也就是滚动条距离底部的距离，默认为0。

因此，我的想法是加一个`infinite-scroll-distance = 10` 的安全阀值。



网上还有另一种方案：当滚动到底部时，判断数据是否加载完成，如果为加载完成，则手动触发数据获取方法。（需要后端配合，因为需要后端给出数据是否加载完成）

```js
handleScroll (e){
    //滚动条滚动时，距离顶部的距离
    const scrollTop = e.target.scrollTop;
    //可视区的高度
    const windowHeight = e.target.clientHeight;
    //滚动条的总高度
    const scrollHeight = e.target.scrollHeight;
    //滚动条到底部的条件
    if(scrollTop + windowHeight === scrollHeight){
        // 判断数据是否加载完成、是否正在加载
        if (!this.allLoaded && !this.loading){
            // 手动获取数据方法
            // 在请求数据方法中，将loading修改为true，防止重复触发，在请求数据结束后，将loading修改为false
            // 在所有数据请求完成后，将allLoaded修改为true 
            this.load();
        }
    }
}
```



（完）

