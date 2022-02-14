---
title: jQuery中的done() fail() then() $when()到底是什么
tags: jQuery
---

1. 目录
{:toc}

<!--more-->

ajax的传统写法：

```js
$.ajax({
    url: "test.html",
    success: function(){
        alert("哈哈，成功了！");
    },
    error:function(){
        alert("出错啦！");
    }
});
```

> Jquery版本在1.5之前，返回的是XHR对象；当版本高于1.5之后，返回的是deferred对象，可以使用 done 和 fail。

所以新的写法如下：

```js
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

可以有多个done，按照顺序执行。

```js
$.ajax("test.html")
　　.done(function(){ alert("哈哈，成功了！");} )
　　.fail(function(){ alert("出错啦！"); } )
　　.done(function(){ alert("第二个回调函数！");} );
```



有时为了省事，可以把done()和fail()合在一起写，这就是then()方法。

```js
$.ajax("test.html")
	.then(function(){ alert("哈哈，成功了！"); },
          function(){ alert("出错啦！"); }
         );
```



`$.when`为多个事件指定相同的回调：

```js
$.when($.ajax("test1.html"), 
       $.ajax("test2.html"))
　　		.done(function(){ alert("哈哈，成功了！"); })
　　		.fail(function(){ alert("出错啦！"); });
```



将普通的异步函数改装成deferred对象来使用$.when：

```js
var wait = function(){
    setTimeout(function(){
　　　　alert("执行完毕！");
　　},5000);
};
```

在未改装前使用无效：**（原因在于$.when()的参数只能是deferred对象）**

```js
$.when(wait())
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```

对wait()函数进行改装：

```js
var wait = function(){
    let df = $.Deferred(); // 新建一个deferred对象
    setTimeout(function(){
        alert("执行完毕！");
        df.resolve(); // 将df对象的执行状态从"未完成"改为"已完成"，从而触发done()方法。
        // df.reject(); // 将df对象的执行状态从"未完成"改为"已失败"，从而触发fail()方法。
　　},5000);
  
  return df;  // 现在返回的就是deferred对象了
};
```

然后就可以使用了：

```js
$.when(wait())
　　.done(function(){ alert("哈哈，成功了！"); })
　　.fail(function(){ alert("出错啦！"); });
```


## 参考链接

http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html



（完）

