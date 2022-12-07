---
layout: mypost
title: 正则表达式过滤html标签只留下文本
tags: JavaScript
---

```js
str.replace(/(.*)<div>/, '$1').replace(/(.*)<p>/, '$1')
```

方法一

```js
function setContent(str) {
  str = str.replace(/<\/?[^>]*>/g,''); //去除HTML tag
  str.value = str.replace(/[ | ]*\n/g,'\n'); //去除行尾空白
  str = str.replace(/\n[\s| | ]*\r/g,'\n'); //去除多余空行
  return str;
}
```

方法一优化
```js
function removeHTMLTag(str) {
    str = str.replace(/<\/?[^>]*>/g,''); //去除HTML tag
    str = str.replace(/[ | ]*\n/g,'\n'); //去除行尾空白
    str = str.replace(/\n[\s| | ]*\r/g,'\n'); //去除多余空行
    str=str.replace(/&nbsp;/ig,'');//去掉&nbsp;
    return str;
}
```

方法二
```js
//去除html标签
function deleteHtmlTag(str){
 str = str.replace(/<[^>]+>|&[^>]+;/g,"").trim();//去掉所有的html标签和&nbsp;之类的特殊符合
 return str;
}
```

方法三

```js
description.replace(/<(?!img).*?>/g, "");  
 
//如果保留img,p标签，则为：
description.replace(/<(?!img|p|/p).*?>/g, ""); 
```

> 参考：https://blog.csdn.net/M82_A1/article/details/90412673