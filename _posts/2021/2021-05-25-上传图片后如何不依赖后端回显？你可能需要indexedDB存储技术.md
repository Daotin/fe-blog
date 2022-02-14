---
title: 上传图片后如何不依赖后端回显？你可能需要indexedDB存储技术
tags: JavaScript
---

1. xxx
{:toc}

大家好，我是前端队长Daotin，想要获取更多前端精彩内容，关注我，解锁前端成长新姿势。

> 全文阅读大概需要8分钟，建议先收藏后看。

<!--more-->


以下正文：

今天看到有人在群里提问说，有一个业务场景，用户上传图片后，图片要回显，不依赖后端，刷新浏览器后也会显示，我是存放在`localStorage`里面，如果图片超过5MB就超过最大存储了，有没有什么办法？

首先他这个问题让我想到，在开发项目的时候的一些对于上传图片后，图片回显的操作，这里我进行总结一下。

## 一、依赖后端的图片回显

一般都是在图片上传后（不清楚如果上传图片的可以参考这篇文章：[前端如何上传文件](https://juejin.cn/post/6964566922037985316)），后端会给我们返回一个上传成功后的图片地址，然后我们用该地址替换到img标签的src即可，这是常规操作。

## 二、不依赖后端，图片一次性回显

不依赖后端就是图片上传后，图片的预览不使用后端返回的图片地址，而是前端通过上传的图片自己显示。
图片`一次性回显`的意思是，在上传成功后回显，但是刷新界面后，图片就不显示了，相当于只是临时看看当时上传的图片。

这种方式操作很简单，有两种方式。

### 1、采用FileReader读取base64格式的图片

一般图片上传，我们会采用`formData`的形式上传到服务器。于是formData形式的数据，我们可以使用`FileReader`来读取到base64格式的图片进行显示。

示例代码如下：

```html
<body>
    <img src="" alt="">
    <input type="file">
</body>
```

```js
// js代码
var inputDom = document.querySelector('input');
var imgDom = document.querySelector('img');

inputDom.onchange = function () {

    // 创建一个formData对象，后期通过ajax上传到服务器
    let formData = new FormData();
    formData.append("iFile", this.files[0]);

    let file = formData.get('iFile');
    console.log('==>', file);

    // 后期取到file文件
    let reader = new FileReader();
    let fileType = file.type;

    // 调用reader进行读取
    reader.readAsDataURL(file);

    // reader读取完成
    reader.onload = function (e) {
        if(/^image\/[jpeg|png|gif]/.test(fileType)) {
            imgDom.src = e.target.result;
        }
    }
}
```

下图是img的src：


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dbd68ffb47e64210965842ccd9a6365f~tplv-k3u1fbpfcp-watermark.image)


## 2、采用createObjectURL函数，使用对象URL显示图片
createObjectURL函数可以创建一个引用任何数据的简单URL字符串。然后用这个url作为img的src即可进行图片回显。

代码很简单，如下：

```js
var inputDom = document.querySelector('input');
var imgDom = document.querySelector('img');

inputDom.onchange = function () {
    // 创建一个本地file文件的临时url
    var objectURL = window.URL.createObjectURL(this.files[0]);
    console.log(src);
    imgDom.src = src;
}
```

下图是img的src：


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a300aa691b7468182a968ccbdce221e~tplv-k3u1fbpfcp-watermark.image)


参考链接：
- https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL
- [显示用户选择的图片的缩略图](https://developer.mozilla.org/zh-CN/docs/Web/API/File/Using_files_from_web_applications#%E4%BE%8B%E5%AD%90%EF%BC%9A%E6%98%BE%E7%A4%BA%E7%94%A8%E6%88%B7%E9%80%89%E6%8B%A9%E7%9A%84%E5%9B%BE%E7%89%87%E7%9A%84%E7%BC%A9%E7%95%A5%E5%9B%BE)


## 三、不依赖后端，图片永久回显

图片永久回显就是页面刷新后，图片依然回显。

目前可以采用的方式为`localStorage`存储在本地，但是如果图片数据过大（*大于10M，目前浏览器localStorage 在 2.5MB 到 10MB 之间*），那么就需要一种新的解决方案，那就是本文的主角：**`indexedDB`**。

通俗地说，`IndexedDB` 就是浏览器提供的本地数据库，它可以被 JavaScript 脚本创建和操作。IndexedDB 允许储存大量数据，提供查找接口，还能建立索引。这些都是 localStorage 所不具备的。

`IndexedDB` 的数据是存在`硬盘`上的，具体有多大呢？Chrome 对IndexedDB 储存空间限制的定义是：硬盘可用空间的三分之一。

> 在IndexedDB之前，还有个WebSQL 数据库，但是W3C组织在2010年11月18日废弃了webSql。尽管两者都是存储的解决方案，但是他们提供的不是同样的功能。IndexedDB 和WebSQL的不同点在于WebSQL 是关系型数据库访问系统，IndexedDB 是索引表系统(key-value型)。
>
> 至于为什么会被废弃，可以参考这篇文章：[HTML5 indexedDB前端本地存储数据库实例教程](https://www.zhangxinxu.com/wordpress/2017/07/html5-indexeddb-js-example/)



### IndexedDB 基本用法

IndexedDB的基本操作可以参考阮一峰老师写的：[浏览器数据库 IndexedDB 入门教程](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html)，非常详细，这里我就不必在重复一遍了。


阮一峰老师写的IndexedDB 操作教程都是基于原生IndexedDB API进行操作的，有时候是比较繁琐的，那有没有一些成熟的封装好的js库供我们使用呢？

答案当然是有的，还不少呢，有了这些封装库，我们可以更快更简单进行IndexedDB的操作。


### indexedDB 兼容性
基本上`IE10+`都支持。https://caniuse.com/?search=indexedDB

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92e9840e7245492fbaa13763a3b5f0a8~tplv-k3u1fbpfcp-watermark.image)



### IndexedDB 封装库推荐

**1、localForage （19K star）**

localForage是一个快速，简单的JavaScript存储库。 localForage通过使用简单的类似于localStorage的API使用异步存储（IndexedDB或WebSQL）来改善Web应用程序的离线体验。localForage在不支持IndexedDB或WebSQL的浏览器中会自动使用localStorage。

Github地址：https://github.com/localForage/localForage

~~**2、PouchDB（14.1K star）**~~

PouchDB是一个受Apache CouchDB启发的开源JavaScript数据库，旨在在浏览器中良好运行。
PouchDB的创建是为了帮助Web开发人员构建脱机工作以及在线工作的应用程序。
它使应用程序可以在脱机时在本地存储数据，然后在应用程序重新联机时将其与CouchDB和兼容服务器同步，从而使用户的数据无论在下次登录时都保持同步。

**（感觉像是在线办公软件的临时离线场景，不适用于本节意义上的纯离线场景）**

Github地址：https://github.com/pouchdb/pouchdb

**3、Dexie.js（6.6K star）**

Dexie.js是indexedDB的封装库。

Github地址：https://github.com/dfahlander/Dexie.js

**4、idb（3.7K star）**

这是一个很小的库（大约1.09k），主要反映了IndexedDB API，但是有一些小的改进，对可用性产生了很大的影响。

Github地址：https://github.com/jakearchibald/idb

（完）

### `(added in 20211231)`

衍生阅读：
- [Why IndexedDB is slow and what to use instead](https://rxdb.info/slow-indexeddb.html)






