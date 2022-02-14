---
title: 如何设置input输入框的宽度随文字的输入长度而改变？
tags: css
---

1. 目录
{:toc}


如何实现下面input输入框的宽度随文字的输入长度而改变？

![GIF](https://user-images.githubusercontent.com/23518990/70308909-3f2ed580-1847-11ea-8704-035c24956b33.gif)


<!--more-->

我们知道，`<input>` 输入框，如果不给一个宽度的话，那么它的宽度是固定的，想让它伸展是不可能的，那怎么办呢？


## 方法

我们可以给input外面套一层div，设置**input的宽高为100%**，让它随着父元素宽度改变而改变。那父元素div的宽高如何确定呢？

再在这个父元素div中塞一个span标签，**用span的宽高撑起div的宽高**。由于span标签的宽高就可以根据它内部的内容的宽高动态改变，所以div的宽高就可以动态改变，然后input的宽高也可以动态改变。

然后再**将span的内容和input 的内容同步**，再将span标签隐藏在input下面，暗搓搓的操纵全局。



## 代码

```html
<div class="add-tag tag-item">
    <span>{{ inputValue }}</span>
    <input  v-model="inputValue" />
</div>

<style>
     .add-tag {
        height: 32px;
        position: relative;

        span {
            display: inline-block;
            width: 100%;
            height: 100%;
            visibility: hidden;
        }

        input {
            width: 100%;
            height: 100%;
            display: inline-block;
            position: absolute;
            left: 0;
            top: 0;
            border: none;
            border-bottom: 1px dashed #ccc;
        }
    }
</style>
```




（完）

