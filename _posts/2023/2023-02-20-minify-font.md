---
layout: mypost
title: 字体压缩方案探索
tags: 前端工程化
---

## 背景

在前端项目开发中，有时候因为系统字体不好看，大多数时候需要自定义字体进行前端展示。

之前我的项目中直接引入了 8M 的思源黑体，导致系统首屏加载时间过长，所以想着如何进行优化，于是有了这篇文章。

## 方案对比

1、字体外链

缺点：收费，而且私有化部署的项目不能使用。

2、单纯字体压缩

能够压缩的体积太小，不太实用。

参考：[https://www.fontsquirrel.com/tools/webfont-generator](https://www.fontsquirrel.com/tools/webfont-generator)

3、使用字蛛 font-spider

font-spider 虽然好用，但是由于其原理是直接分析本地 CSS 与 HTML 文件获取所有项目中使用过的字符，然后将这部分字符做成字体引入使用。

但是这样一来,对于动态生成的文字，就没有办法使用 font-slider 了。

尤其在当下，很多框架都是数据驱动的，更是很多文字都不会直接出现 html 文件中，所以 font-spider 就不能适用了。

参考：[https://juejin.cn/post/7145757440070385694](https://juejin.cn/post/7145757440070385694)

4、Fontmin

通过自定义需要提取的文字，来压缩问题体积。

前端项目可以使用遍历文件的方式获取所有文字进行提起。

缺点：

这个方案只是针对本地（项目下）的文件进行扫描，对于从后端返回来的数据是没办法实现的，就比如后端获取用户名、富文本编辑器里面 UGC 文字等等。

参考：[https://juejin.cn/post/7123041200449257480](https://juejin.cn/post/7123041200449257480)

5、考虑将常用的汉字和英文字符等生成一个字体文件（预计 1M 左右）

参考：[https://github.com/DeronW/minify-font](https://github.com/DeronW/minify-font)

另外：

- OFT/TTF 的兼容性更好，但是其字体文件体积最大。
- WOFF 字体比 TTF 字体有更小的体积和更好的表现性。

> WOFF 字体均经过 WOFF 的编码工具压缩，文件大小一般比 TTF 小 40%，加载速度更快，可以更好的嵌入网页中。**目前主流的浏览器的新版本几乎都支持 WOFF。**WOFF2 是 WOFF 的下一代。 WOFF2 格式在原有的基础上提升了 30% 的压缩率。由于它还没有 WOFF 的广泛支持，所以还只是一个可展望的升级。

## 解决方案

采用 WOFF/WOFF2 格式的常用的汉字(3500/7000 个)，英文，字符的文件。

优化前字体大小 8000k，压缩后字体大小 800k，减少 10 倍。

然后再转为 woff2 格式，体积能再减少近 1 倍。

## 结果与 demo

基于 [https://github.com/DeronW/minify-font](https://github.com/DeronW/minify-font) 项目进行修改，生成自己项目需要的 woff2 文件。

dist 文件夹已经生成好了思源黑体字体，可直接下载使用。

项目地址：[https://github.com/Daotin/minify-font](https://github.com/Daotin/minify-font)

优化后的结果如下：

![](/image/2023/2023-08-04-14-43-00.png)
