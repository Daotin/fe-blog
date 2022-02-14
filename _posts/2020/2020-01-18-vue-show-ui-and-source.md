---
title: vue组件编写文档如何一份代码既显示UI样式，又显示文件源代码？
tags: vue
---

1. 目录
{:toc}


## 前言

在编写组件文档的时候，需要在一个页面同时展示组件的UI样式和编写组件的源代码。

最简单的做法是写两份代码，一份展示UI，一份展示源代码，但是这样维护起来很麻烦，每次改动都要修改两份代码。

<!--more-->


## 问题描述

有没有好方法可以在只写一份代码的情况下实现上述功能呢？

有当然是有，不过踩了一些坑。

## 问题分析

最开始我想的是通过js获取UI展示部分的代码，然后通过js创建元素，添加到文件末尾。

但是这有一个问题是，只能获取到html元素，对于script的部分不能显示，也就对整个组件的使用方法不是很清晰。

于是我就换个思路，获取整个vue文件的源代码，然后显示。

这个思路是可行的，唯一的问题是如何获取整个vue文件的源码。

## 解决方案

通过 Google 找到了一个解决方案：**通过使用 ajax 来获取本地vue文件。**

具体代码如下，详细步骤见注释：

```js
function getCode(fileName) {
    let src = `/source/app/docs/${fileName}.vue`;
    /**
     * <div class="docs-code"></div> 就是展示代码的区域
     * 至于为什么加<pre><code></code></pre>? 
     * 1、是因为高亮显示使用了 highlight.js插件，这个插件要求<pre><code></code></pre> 之前的代码才会被高亮。
     * 2、并且因为有了pre标签，所以代码中的空格换行等格式才得以保留。
     */
    let docsCode = $('<div class="docs-code"><pre><code></code></pre></div>');

    // 创建一个新的xhr对象
    let xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');
    xhr.open('GET', src);
    // 指定返回的数据为纯文本格式
    xhr.responseType = 'text'; // 如果不兼容，可采用 xhr.overrideMimeType('text/plain;charset=utf-8'); 
    xhr.send();
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4 && xhr.status === 200) {
            // 将获取的vue文件源码插入到code标签中
            docsCode.find('code').text(xhr.responseText);
            // 将代码插入到vue文件尾部
            docsCode.appendTo($('.' + fileName));
            // 代码高亮显示函数
            Vue.prototype.$highlight();
        } else {
            docsCode.find('code').text('获取文件源代码失败。');
        }
    };
}

```
如此，我的一份代码即可显示组件UI，亦可显示组件源代码本身。


我把代码高亮和获取封装成vue插件的形式，在每个vue组件中的mounted调用即可：

```js
import Vue from 'vue';
import Hljs from 'highlight.js';
import 'highlight.js/styles/github.css';

let Highlight = {};
Highlight.install = function (Vue, options) {
    Vue.prototype.$highlight = () => {
        $('pre code').each(function (i, element) {
            // element.innerHTML = element.innerHTML.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;").replace(/'/g, "&#039;");
            Hljs.highlightBlock(element);
        });
    };

    // 获取docs-button.vue源代码
    Vue.prototype.$getCode = (name) => {
        let src = `/source/app/docs/${name}.vue`;
        // 有了pre可以显示源代码中的空格和换行
        let docsCode = $('<div class="docs-code"><pre><code></code></pre></div>');

        // 创建一个新的xhr对象
        let xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHTTP');
        xhr.open('GET', src);
        xhr.responseType = 'text';
        xhr.send();
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4 && xhr.status === 200) {
                docsCode.find('code').text(xhr.responseText);
                docsCode.appendTo($('.' + name));
                // 代码高亮显示
                Vue.prototype.$highlight();
            } else {
                docsCode.find('code').text('获取文件源代码失败。');
            }
        };
    };
};
export default Highlight;

```


示例如下图：
![image](https://user-images.githubusercontent.com/23518990/72592219-12d5a100-393d-11ea-9f95-91551c60b232.png)

## 参考链接

- https://blog.csdn.net/SilenceJude/article/details/97002176



（完）


