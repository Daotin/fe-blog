---
title: 聊聊HTML5中的Web Notification桌面通知
tags: HTML
---

1. 目录
{:toc}


有的时候我们会在桌面右下角看到这样的提示：

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155031.png)

这种桌面提示是HTML5新增的 `Web Push Notifications` 技术。

<!--more-->

Web Notifications 技术使页面可以发出通知，通知将被显示在页面之外的系统层面上。能够为用户提供更好的体验，即使用户忙于其他工作时也可以收到来自页面的消息通知，例如一个新邮件的提醒，或者一个在线聊天室收到的消息提醒等等。

> PS：除了IE外，各大现代浏览器都对这个桌面推送有了基本的支持。



## 开始

要创建一个消息通知，非常简单，直接使用 window 对象下面的`Notification`类即可，代码如下：

```js
var n = new Notification("桌面推送", {
	icon: 'img/icon.png',
	body: '这是我的第一条桌面通知。',
    image:'img/1.jpg'
});
```

于是你就会看到系统桌面弹出我上面那张截图的通知。

> PS：消息通知只有通过`Web服务访问`该页面时才会生效，如果直接双击打开本地文件，是没有任何效果的。也就是说你的文件需要使用服务器的形式打开，而不是直接使用浏览器打开本地文件。

当然也有可能你什么都没看到，别着急继续往下看。



## 基本语法

当然在想要弹出上面通知之前，有必要了解一下一个通知的基本语法：

```js
let myNotification = new Notification(title, options);
```

`title`：定义一个通知的标题，当它被触发时，它将显示在通知窗口的顶部。

`options`（可选）对象包含应用于通知的任何自定义设置选项。

常用的选项有：

- body: 通知的正文，将显示在标题下方。

- tag: 类似每个通知的ID，以便在必要的时候对通知进行刷新、替换或移除。

- icon:  显示通知的图标

- image: 在通知正文中显示的图像的URL。

- data: 您想要与通知相关联的任意数据。这可以是任何数据类型。

- renotify: 一个 Boolean 指定在新通知替换旧通知后是否应通知用户。默认值为false，这意味着它们不会被通知。

- requireInteraction: 表示通知应保持有效，直到用户点击或关闭它，而不是自动关闭。默认值为false。



当这段代码执行时，浏览器会询问用户，是否允许该站点显示消息通知，如下图所示：

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155108.png)

只有用户点击了`允许`，授权了通知，通知才会被显示出来。



## 授权

*如何获取到用户点击的是“允许”还是“阻止”呢？*



window的 Notification实例有一个 `requestPermission` 函数用来获取用户的授权状态：

```js
// 首先，我们检查是否具有权限显示通知
  // 如果没有，我们就申请权限
  if (window.Notification && Notification.permission !== "granted") {
      Notification.requestPermission(function (status) {
      //status是授权状态。
      //如果用户点击的允许，则status为'granted'
      // 如果用户点击的禁止，则status为'denied'
     
      // 这将使我们能在 Chrome/Safari 中使用 Notification.permission
      if (Notification.permission !== status) {
        Notification.permission = status;
      }
    });
  }
```

> 注意：如果用户点击了授权右上角的关闭按钮，则status的值为default。

之后，我们只需要判断 status 的值是否为`granted`，来决定是否显示通知。



## 通知事件

但是单纯的显示一个消息框是没有任何吸引力的，所以消息通知应该具有一定的交互性，在显示消息的前前后后都应该有事件的参与。

Notification一开始就制定好了一系列事件函数，开发者可以很方面的使用这些函数处理用户交互：

有：`onshow`,`onclick`,`onerror`,`onclose`。

```js
var n = new Notification("桌面推送", {
	icon: 'img/icon.png',
	body: '这是我的第一条桌面通知。'
});
 
//onshow函数在消息框显示时触发
//可以做一些数据记录及定时关闭消息框等
n.onshow = function() {
	console.log('显示消息框');
	//5秒后关闭消息框
	setTimeout(function() {
		n.close();
	}, 3000);
};
 
//消息框被点击时被调用
//可以打开相关的视图，同时关闭该消息框等操作
n.onclick = function() {
	console.log('点击消息框');
	// 打开相关的视图
	n.close();
};
 
//当有错误发生时会onerror函数会被调用
//如果没有granted授权，创建Notification对象实例时，也会执行onerror函数
n.onerror = function() {
	console.log('消息框错误');
	// 做些其他的事
};
 
//一个消息框关闭时onclose函数会被调用
n.onclose = function() {
	console.log('关闭消息框');
	//做些其他的事
};
```



## 一个简单的例子

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Document</title>
  </head>
  <body>
    <button>点击发起通知</button>
  </body>
  <script>
    window.addEventListener("load", function() {
      // 首先，让我们检查我们是否有权限发出通知
      // 如果没有，我们就请求获得权限
      if (window.Notification && Notification.permission !== "granted") {
        Notification.requestPermission(function(status) {
          if (Notification.permission !== status) {
            Notification.permission = status;
          }
        });
      }

      var button = document.getElementsByTagName("button")[0];

      button.addEventListener("click", function() {
        // 如果用户同意就创建一个通知
        if (window.Notification && Notification.permission === "granted") {
          var n = new Notification("Hi！");
        }

        // 如果用户没有选择是否显示通知
        // 注：因为在 Chrome 中我们无法确定 permission 属性是否有值，因此
        // 检查该属性的值是否是 "default" 是不安全的。
        else if (window.Notification && Notification.permission !== "denied") {
          Notification.requestPermission(function(status) {
            if (Notification.permission !== status) {
              Notification.permission = status;
            }

            // 如果用户同意了
            if (status === "granted") {
              var n = new Notification("Hi!");
            }

            // 否则，我们可以让步的使用常规模态的 alert
            else {
              alert("Hi!");
            }
          });
        }

        // 如果用户拒绝接受通知
        else {
          // 我们可以让步的使用常规模态的 alert
          alert("Hi!");
        }
      });
    });
  </script>
</html>
```

当我们打开界面的时候，就会弹出授权申请，如果我们点击`允许`，然后点击按钮，就可以发送一条通知到桌面，我们就可以在桌面右下角看到这条通知。

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155403.png)

上面我们只是显示一条消息。

```js
if (status === "granted") {
  var n = new Notification("Hi");
}
```

如果我们有很多消息的话，比如我是用个for循环来模拟大量通知的情况。

```js
for(var i=0; i<10; i++) {
    var n = new Notification("Hi,"+i);
}
```

可以看到有10条通知都显示出来。但是某些情况下对于用户来说，显示大量通知是件令人痛苦的事情。

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155632.png)

比如，如果一个即时通信应用向用户提示每一条传入的消息。为了避免数以百计的不必要通知铺满用户的桌面，可能需要接管一个挂起消息的队列。

因此，需要为新建的通知添加一个`标记`。


如果有一条新通知和上一条通知具有相同的标记，那么这条新通知将会替换上一条通知，最后桌面上只会显示最新的通知。


还是上面的例子，只需要在触发通知加个`tag`属性即可：

```js
for (var i = 0; i < 10; i++) {
   // 最后只看到内容为 "Hi! 9" 的通知
    var n = new Notification("Hi! " + i, {tag: 'soManyNotification'});
}
```



## 最后

消息通知是个不错的特性，可是也不排除有些站点恶意的使用这个功能，一旦用户授权之后，不时的推送一些不太友好的消息，打扰用户的工作，这个时候我们可以移除该站点的权限，禁用其消息通知功能。

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155205.png)

我们可以点击浏览器地址输入框左边的叹号就有一个通知的选项，我们可以修改授权。或者在通知页面也有修改通知的选项，可以根据具体情况进行修改授权通知。

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190727155225.png)



于是最基本的 Web Notification 就实现了。




（完）

