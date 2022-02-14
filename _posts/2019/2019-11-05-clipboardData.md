---
title: 使用JS直接上传并预览粘贴板的图片
tags: JavaScript
img: https://user-images.githubusercontent.com/23518990/70222821-85bbfb80-1785-11ea-82ae-f2e6baaf48c4.png
---

1. 目录
{:toc}

## 提出需求

因为工作原因，现在有一个需求就是需要用户使用QQ或者微信复制一张截图后，在div中直接粘贴这张图片，而不是采用上传的方式。类似我们在使用QQ微信时直接粘贴截图的操作，这个要怎么用js来实现呢？

<!--more-->

## 实现原理

我们可以利用 `Clipboard` 这个接口API 来实现。

根据在MDN上的定义，Clipboard接口提供了一种**读写操作系统剪贴板**的方式。这样我们就可以获取剪贴板的内容，然后通过js插入到某个元素中。

具体Clipboard API的MDN链接如下： https://developer.mozilla.org/zh-CN/docs/Web/API/Clipboard 

在浏览器兼容性上面，可以看到大体上，常用的浏览器都支持，不过IE就就只能是IE11及以上了。

![image-20191205162813463](https://user-images.githubusercontent.com/23518990/70222796-75a41c00-1785-11ea-9426-5289166bf52d.png)



## 获取剪贴板的图片

直接上代码了。

```js
//  将粘贴事件绑定到 document 上
document.addEventListener("paste", function (e) {

    let items = event.clipboardData && event.clipboardData.items;
    let file = null;
    if (items && items.length) {
        // 检索剪切板items中类型带有image的
        for (var i = 0; i < items.length; i++) {
            if (items[i].kind === 'file') { // 或者 items[i].type.indexOf('image') !== -1
                file = items[i].getAsFile(); // 此时file就是剪切板中的图片文件
                break;
            }
        }
    }
}, false); 
```

如果复制的是文本的话，可以这样或者粘贴板的文本内容：

```js
let text = null;
if (items && items.length) {
    // 检索剪切板items
    for (let i = 0; i < items.length; i++) {
        // ...
        if (items[i].kind === 'string') {
            text = event.clipboardData.getData('Text'); // 获取文本内容
            break;
        }
    }
}
```

获取到的是文本内容，可以直接在前端显示。

如果是图片的话，就需要上传到服务器，然后再在前端预览，具体操作往下看。

## 上传到服务器

如果只是图片，可以直接使用ajax将file保存到服务器即可。

或者通过 FormData 对象进行ajax上传。

```js
let formData = new FormData();
formData.append('file', file); // 将formData上传即可
```

下次下载之后，通过get方法再次获取到file文件。

```js
let file = formData.get('file'); 
```



## 前端显示预览

上传成功后，我们需要及时在前端看到这个图片，这个可以通过`FileReader`对象就可以做到。 

```js
let reader = new FileReader();

reader.readAsDataURL(file);

reader.onload = function (e) {
    let img = document.createElement("img");
    img.src = e.target.result;
    document.body.appendChild(img); // 将图片插入body中
}
```

或者在html中定义好`<img>`标签，直接修改图片的src即可

```js
reader.onload = function (e) {
    let img = document.getElementByName("img")[0];
    img.src = e.target.result;
}
```

> 注意：这里`e.target.result` 得到的是图片的Base64格式的地址。



## 示例demo

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        <title>Document</title>
    </head>
    <body>
        <script>
            //  将粘贴事件绑定到 document 上
            document.addEventListener(
                'paste',
                function(e) {
                    let items = event.clipboardData && event.clipboardData.items;
                    let file = null;
                    if (items && items.length) {
                        for (let i = 0; i < items.length; i++) {
                            if (items[i].type.indexOf('image') !== -1) {
                                file = items[i].getAsFile(); // 此时file就是剪切板中的图片文件
                                break;
                            }
                        }
                    }
                    // 如果需要上传后端，只需使用ajax将file上传即可。
                    // 这里是ajax上传操作...

                    if (file.size === 0) {
                        return;
                    }
					
                    // 上传成功后，在回调函数中设置前端预览
                    let reader = new FileReader();

                    reader.readAsDataURL(file);

                    reader.onload = function(e) {
                        let img = document.createElement('img');
                        img.src = e.target.result;
                        document.body.appendChild(img);
                    };
                },
                false
            );
        </script>
    </body>
</html>

```

可以直接复制上面demo代码进行实验。


（题图：梵高-橄榄树）

（完）

