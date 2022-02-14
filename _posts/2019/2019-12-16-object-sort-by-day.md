---
title: 将包含时间戳的对象数组按天排序
tags: JavaScript
---

1. 目录
{:toc}



## 问题描述

示例对象数组如下，每个对象中都有一个时间戳，现在要求将每个对象按照其中的时间戳对应的天数进行排列，如何实现？

<!--more-->


## 需求分析

示例代码如下：

```js
var list = [
    {
        time: 1525681075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页'
    },
    {
        time: 1555581075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页dadasdasdadasdasdsdas'
    },
    {
        time: 1565681075426,
        curURL:
            'http://www.baidu.com?dsadasdasjfodfjsodifuosdfuosdfjuosdfi',
        title: '百度首页1'
    }
    {
        time: 1544681075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页哈哈哈哈哈哈哈哈哈哈哈'
    },
    
    
];
```

### 1、数组排序

首先，需要先**将上面的对象数组按照时间戳有小到大排好序**。排序函数：

```js
let list = list.sort(function(a, b) {
    return a.time - b.time;
});
```


排好序的对象数组如下：


```js
var list = [
    {
        time: 1525681075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页'
    },
    {
        time: 1544681075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页哈哈哈哈哈哈哈哈哈哈哈'
    },
    {
        time: 1555581075426,
        curURL: 'http://www.baidu.com',
        title: '百度首页dadasdasdadasdasdsdas'
    },
    {
        time: 1565681075426,
        curURL:
            'http://www.baidu.com?dsadasdasjfodfjsodifuosdfuosdfjuosdfi',
        title: '百度首页1'
    }
];
```



### 2、封装函数

首先将第一个时间戳转化成日期，然后循环遍历后面的时间戳，对比日期是否相同，由于时间戳都是按照从小到大的顺序排列的，所以比较新时间戳的时候，只需要与排好的日期的最后一个日期进行对比，如果在最后一个日期以内就加到这个时间戳对应的日期数组中去去，如果不在就往后面日期排，以此类推。

代码如下：


```js
formatDate(sortedDateList) {
    var arr = [];
    sortedDateList.forEach(function(item, i) {
        var tmpDate = new Date(item.time);
        var day = tmpDate.getDate();
        var month = tmpDate.getMonth() + 1;
        var year = tmpDate.getFullYear();
		
        // 首先取第一个时间戳（也是最小的时间戳）
        if (i === 0) {
            var tmpObj = {};
            tmpObj.date = year + '-' + month + '-' + day; // 时间戳对应的日期
            tmpObj.dataList = [];  // 存储相同时间戳日期的数组
            tmpObj.dataList.push(item);
            arr.push(tmpObj);
        } else {
            // 判断两个时间戳对应的日期是否相等，相等就加进去，不相等就另开辟新的时间戳日期
            if (arr[arr.length - 1]['date'] === year + '-' + month + '-' + day) {
                arr[arr.length - 1]['dataList'].push(item);
            } else {
                var tmpObj = {};
                tmpObj.date = year + '-' + month + '-' + day;
                tmpObj.dataList = [];
                tmpObj.dataList.push(item);
                arr.push(tmpObj);
            }
        }
    });
    return arr;
}
```



转化之后的结构如下：

```js
let recordList =  [
    {
        date: '2018-12-5',
        dataList: [
            {
                time: 1525681075426,
                curURL: 'http://www.baidu.com',
                title: '百度首页'
            },
            {
                time: 1524681075426,
                curURL: 'http://www.baidu.com',
                title: '百度首页哈哈哈哈哈哈哈哈哈哈哈'
            },
            {
                time: 1525581075426,
                curURL: 'http://www.baidu.com',
                title: '百度首页dadasdasdadasdasdsdas'
            }
        ]
    },
    {
        date: '2019-8-13',
        dataList: [
            {
                time: 1545681075426,
                curURL:
                    'http://www.baidu.com?dsadasdasjfodfjsodifuosdfuosdfjuosdfi',
                title: '百度首页1'
            }
        ]
    }
    ];
```


（完）

