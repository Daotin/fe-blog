---
title: less导入之multiple关键字
tags: vue less 
---

1. 目录
{:toc}


## 问题描述
之前我写了一个自定义滚动条的样式文件`custom_scroll.less`，然后在Vue的代码中引用

```less
@import '../../resources/less/custom_scroll.less';
```

<!--more-->


但是一个Vue中，只能 `@import` 一次，也就是相同文件只会被导入一次，而随后的导入文件的语句都会被忽略。

比如元素A使用了 `@import`  ，元素B再使用 `@import`  就会失效。

比如下面代码中，subject-list的自定义滚动条样式是不生效的：

```css
.category-manager {
        width: 400px;
        height: calc(~'100% - 60px');
        float: left;
        border: 1px solid #ddd;
        overflow: auto;
        @import '../../resources/less/custom_scroll';
    }

    .subject-list {
        height: calc(~'100% - 60px');
        margin-left: 410px;
        border: 1px solid #ddd;
        overflow: auto;
        @import '../../resources/less/custom_scroll';
    }
```

当时我没有找到原因，于是我又写了一个 `custom_scroll-all.less`  文件，让它下面的所有元素的滚动条样式改变。这样可以达到多个元素都可以使用自定义滚动条。

比如C是A和B的父元素，那么在C中 @import 一次 `custom_scroll-all.less` 文件即可。

## 解决方法

今天突然看到一个下面的写法：

```less
@import (multiple) '../../resources/less/custom_scroll';
```


然后去查了资料才知道，@import 其实是 `@import (once)` 的简写。**表示相同文件只会被导入一次，而随后的导入文件的语句都会被忽略。**

```less
@import (once) "foo.less";
@import (once) "foo.less"; // this statement will be ignored
```



解决办法就是使用：**@import (multiple)**

使用`@import (multiple)`允许导入多个同名文件。这与只能导入一次的行为是对立的。

比如：

```less
@import (multiple) "foo.less";
@import (multiple) "foo.less";

// 输出两次相同的foo.less代码
.a {
  color: green;
}
.a {
  color: green;
}
```


（完）

