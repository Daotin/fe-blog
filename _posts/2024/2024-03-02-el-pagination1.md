---
layout: mypost
title: el-pagination切换分页调用两次分页接口问题
tags:
  - ElementUI
---

## 问题

我们一般会在 el-pagination 触发 size-change 和 current-change 的时候，都会重新调用分页接口。

那么当数据有 51 条，size=10，page=6，也就是最后一页的时候，缺环分页为 size=50 后，会触发两次分页接口：

- 第一次：size=50，page=6
- 第二次：size=50，page=2

那么，当数据量比较大，接口返回很慢的时候，就会出现第一次的调用结果晚到，导致结果不对。

## 分析

我们可以做一些优化方案：

方案一：

每次切换 size 后，el-pagination 会发现页码不够，自动定位到最后一页，等其定位好后，再进行搜索。

优点：两次的结果是相同的，因为搜索的参数相同了

缺点：依然会搜索两次

方案一（优化）

在方案一的基础上，加入防抖功能，避免两次调用。

```js
export function debounce(func, wait, immediate) {
  let timeout, args, context, timestamp, result

  const later = function() {
    // 据上一次触发时间间隔
    const last = +new Date() - timestamp

    // 上次被包装函数被调用时间间隔 last 小于设定时间间隔 wait
    if (last < wait && last > 0) {
      timeout = setTimeout(later, wait - last)
    } else {
      timeout = null
      // 如果设定为immediate===true，因为开始边界已经调用过了此处无需调用
      if (!immediate) {
        result = func.apply(context, args)
        if (!timeout) context = args = null
      }
    }
  }
}
```

方案二：（⭐ 推荐）

每次切换分页大小，定位到第 1 页。

在 handleSizeChange 里面，将 currentPage = 1（使用 js 手动设置 currentPage，不会触发 handleCurrentChange），由于不管怎么切换分页，第一页永远存在，所以不会触发 handleCurrentChange，只会触发一次接口请求。

优点：只会调用 1 次

缺点：每次切换分页都会定位到第 1 页

方案三：

优化不定位到第 1 页，而是页码不够的时候定位到第 1 页。

这个需要后端分页接口加个参数，当有这个参数的时候，分页只查询总数和总页数。

如果当前的页码小于总页数，则不改变页码定位，否则定位到第 1 页。

优点：不用每次定位到第 1 页

缺点：每次切换分页（分页调大的时候）都会调用两次分页接口

## 隐藏的问题

目前以上的方式都还有有一个隐藏的问题。比如获取最后一页的数据需要 10s，获取第一页的数据需要 2s，如果用户操作很快（先点击最后一页，再点击第一页），必然会出现第一次获取的数据晚到的情况。

但是，由于我们会在接口返回值后，将当前 currentPage 设置为返回值，实际上最后用户看到的还是最后一页的数据。

如果还有避免这种点击了第一页，但是最后还在最后一页的情况，办法就是在返回结果前，禁用第一页的点击。
