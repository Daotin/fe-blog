上一章我们知道，执行一段异步任务，需要先将任务添加到消息队列中。不过通过定时器设置回调函数有点特别，它们需要在指定的时间间隔内被调用，但消息队列中的任务是按照顺序执行的，所以为了保证回调函数能在指定时间内执行，你不能将定时器的回调函数直接添加到消息队列中。



**那么如何设计？**



在 Chrome 中，除了正常使用的消息队列之外，还有另外一个消息队列**延迟队列**，这个队列中维护了需要延迟执行的任务列表，包括了定时器和 Chromium 内部一些需要延迟执行的任务。所以当通过 JavaScript 创建一个定时器时，渲染进程会将该定时器的回调任务添加到延迟队列中。



**现在延时代码是如何执行的呢？**



当通过 JavaScript 调用 setTimeout 设置回调函数的时候，渲染进程将会创建一个回调任务，包含了回调函数自身、当前发起时间、延迟执行时间。然后将这个回调任务放入延迟队列中。接着正常往下执行消息队列中的任务。



在当前消息队列中的任务执行完成后，查看延迟队列的任务延时时间是否到了，如果还没到继续执行下一个消息队列的任务；如果到了，则执行延迟队列的任务。



如果在执行一个消息队列的任务的时候，延迟队列的任务时间到了，那也不能立即执行，得等消息队列的当前任务执行完成后，才能执行这个延迟任务，而且延迟任务的执行也是按顺序执行的，很多个延迟队列的任务时间都到了也得按照先进延迟队列的先执行。



所以，基于上面的原理，如果当前消息队列的任务执行太久，或者延迟队列的任务排名太靠后，那么这个延迟时间就不准了。



## setTimeout注意事项

1、延时不准的问题

2、如果 setTimeout 存在嵌套调用，那么延时时间最短为 4 ms。

（一般如果使用 setTimeout 来做动画的时候，都是需要 setTimeout 嵌套调用，那么对于实时性很高的动画就不适用了。）

3、在未激活的页面中的 setTimeout 执行的最小间隔为 1000ms ，这是浏览器干预的，为了欧化后台页面的加载损耗以及降低耗电量。

4、延时时间有最大值。（ Chrome、Safari、Firefox 都是以 32 个 bit 来存储延时值的，32bit 最大只能存放的数字是 2147483647 毫秒，这就意味着，如果 setTimeout 设置的延迟值大于 2147483647 毫秒（大约 24.8 天）时就会溢出，这导致定时器会被立即执行）

5、如果 setTimeout 的回调函数是一个对象中的一个方法属性，那么这个方法中的 this 会变成 window。

```js
let obj = {
    show: function () {
        console.log(this);
    }
};
setTimeout(obj.show, 100);
```

解决办法：

```js
// 方式一
setTimeout(function () {
    obj.show();
}, 100);

// 方式二
setTimeout(() => {
    obj.show();
}, 100);

// 方式三
setTimeout(obj.show.bind(obj), 100);
```



## 思考题

*requestAnimationFrame 与 setTimeout 区别于联系？*



requestAnimationFrame采用系统时间间隔，保持最佳绘制效率，不会因为间隔时间过短，造成过度绘制，增加开销；也不会因为间隔时间太长，使用动画卡顿不流畅，让各种网页动画效果能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果。



requestAnimationFrame的优势，在于充分利用显示器的刷新机制，比较节省系统资源。显示器有固定的刷新频率（60Hz或75Hz），也就是说，每秒最多只能重绘60次或75次，requestAnimationFrame的基本思想就是与这个刷新频率保持同步，利用这个刷新频率进行页面重绘。此外，使用这个API，一旦页面不处于浏览器的当前标签，就会自动停止刷新。这就节省了CPU、GPU和电力。



语法：

```js
let requestID = window.requestAnimationFrame(callback);

// 取消回调函数
window.cancelAnimationFrame(requestID) 
```

考虑到兼容性：

```js
 window.requestAnimFrame = (function(){
      return  window.requestAnimationFrame       || 
              window.webkitRequestAnimationFrame || 
              window.mozRequestAnimationFrame    || 
              window.oRequestAnimationFrame      || 
              window.msRequestAnimationFrame     || 
              function( callback ){
                window.setTimeout(callback, 1000 / 60);
              };
    })();
```



