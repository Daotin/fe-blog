

先来看个“XMLHttpRequest 运行机制”图：

![image.png](https://i.loli.net/2019/09/21/bn6SyhOXvEYTgGa.png)



## Ajax请求是怎么执行的

Ajax请求的创建就不说了，从发送的时候说起。

当调用“xhr.send();” 的时候：

渲染进程会将请求发送给网络进程，然后网络进程负责资源的下载，等网络进程接收到数据之后，就会利用 IPC 来通知渲染进程；渲染进程接收到消息之后，会将 xhr 的回调函数封装成任务并**添加到消息队列**中（这种机制和setTimeout添加到延迟队列是不同的），等主线程循环系统执行到该任务的时候，就会根据相关的状态来调用对应的回调函数。



## XMLHttpRequest 的一些坑

1、跨域问题，浏览器的同源策略。

2、HTTPS 混合内容

HTTPS 混合内容是 HTTPS 页面中包含了不符合 HTTPS 安全要求的内容，比如包含了 HTTP 资源，通过 HTTP 加载的图像、视频、样式表、脚本等，都属于混合内容。

当**通过 HTML 文件**加载的混合资源，会给出警告，但大部分类型还是能加载的。

![image.png](https://i.loli.net/2019/09/21/atNvKA5mhirluM2.png)

而在HTTPS页面**通过 XMLHttpRequest 请求**相同的HTTP资源（比如上面的图片）时，浏览器认为这种请求可能是攻击者发起的，会阻止此类危险的请求。

![image.png](https://i.loli.net/2019/09/21/rCuVKapUm3LTlwx.png)