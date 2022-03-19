## Promise 引入原因

Promise 解决的是异步编码风格的问题，而不是一些其他的问题。



Promise解决的两个问题：

- **消灭嵌套调用（回调地狱）**
- **合并多个任务的错误处理**



在下面的例子中，有四个 Promise 对象：p0～p4。无论哪个对象里面抛出异常，都可以通过最后一个对象 p4.catch 来捕获异常，通过这种方式可以将所有 Promise 对象的错误合并到一个函数来处理，这样就解决了每个任务都需要单独处理异常的问题。

```js
function executor(resolve, reject) {
    let rand = Math.random();
    console.log(1)
    console.log(rand)
    if (rand > 0.5)
        resolve()
    else
        reject()
}
var p0 = new Promise(executor);

var p1 = p0.then((value) => {
    console.log("succeed-1")
    return new Promise(executor)
})

var p3 = p1.then((value) => {
    console.log("succeed-2")
    return new Promise(executor)
})

var p4 = p3.then((value) => {
    console.log("succeed-3")
    return new Promise(executor)
})

p4.catch((error) => {
    console.log("error")
})
console.log(2)

```



## Promise 与微任务

看下面这个例子：

```js
function executor(resolve, reject) {
    resolve(100)
}
let demo = new Promise(executor)

function onResolve(value){
    console.log(value)
}
demo.then(onResolve)

```

首先执行Promise的构造函数，然后执行executor函数，这时候会触发 demo.then 设置的回调函数 onResolve，所以可以推测，resolve 函数内部调用了通过 demo.then 设置的 onResolve 函数。



我们用 Bromise 函数来模拟 Promise 的这个过程：

```js
function Bromise(executor) {
    var onResolve_ = null
    var onReject_ = null
     // 模拟实现 resolve 和 then，暂不支持 rejcet
    this.then = function (onResolve, onReject) {
        onResolve_ = onResolve
    };
    function resolve(value) {
        onResolve_(value);
    }
    executor(resolve, null);
}

```

我们实现了自己的构造函数、resolve、then 方法。接下来我们使用 Bromise 来实现我们的业务代码，实现后的代码如下所示：

```js
function executor(resolve, reject) {
    resolve(100)
}
// 将 Promise 改成我们自己的 Bromsie
let demo = new Bromise(executor)

function onResolve(value){
    console.log(value)
}
demo.then(onResolve)

```

执行这段代码，我们发现执行出错，输出的内容是：

```
Uncaught TypeError: onResolve_ is not a function
    at resolve (<anonymous>:10:13)
    at executor (<anonymous>:17:5)
    at new Bromise (<anonymous>:13:5)
    at <anonymous>:19:12

```

之所以出现这个错误，是由于 Bromise 的延迟绑定导致的，在调用到 onResolve_ 函数的时候，Bromise.then 还没有执行，所以执行上述代码的时候，当然会报`onResolve_ is not a function`的错误了。也正是因为此，我们要改造 Bromise 中的 resolve 方法，让 resolve 延迟调用 onResolve_。

要让 resolve 中的 onResolve_ 函数延后执行，可以在 resolve 函数里面加上一个定时器，让其延时执行 onResolve_ 函数，你可以参考下面改造后的代码：

```js
function resolve(value) {
    setTimeout(()=>{
        onResolve_(value)
    },0)
}
```

上面采用了定时器来推迟 onResolve 的执行，不过使用定时器的效率并不是太高.

好在我们有微任务，所以在 Promise 实现原理中，把这个定时器改为微任务了，这样既可以让 onResolve_ 延时被调用，又提升了代码的执行效率。这就是 Promise 与微任务的关系了。

