async/await是在ES7中引入的，提供了在不阻塞主线程的情况下使用同步代码实现异步访问资源的能力。

如下面代码写法是同步代码的形式：

```js
async function foo(){
  try{
    let response1 = await fetch('https://www.geekbang.org')
    console.log('response1')
    console.log(response1)
    let response2 = await fetch('https://www.geekbang.org/test')
    console.log('response2')
    console.log(response2)
  }catch(err) {
       console.error(err)
  }
}

foo();

```



## async/await是如何工作的

async/await使用了Generator和Promise两种技术。

Promise已经知道了，那么什么是Generator呢？

### Generator

具体可以看这篇文章：https://gitee.com/daotin/Web/blob/master/12-ES6/05-Generator%EF%BC%8Casync%EF%BC%8CClass.md

Generator 可以实现函数的暂停和恢复，那么是怎么做到的呢？

### 协程

要搞懂函数为何能暂停和恢复，那你首先要了解**协程**的概念。

协程是一种比线程更加轻量级的存在。你可以把协程看成是跑在线程上的任务，一个线程上可以存在多个协程，但是在线程上同时只能执行一个协程，比如当前执行的是 A 协程，要启动 B 协程，那么 A 协程就需要将主线程的控制权交给 B 协程，这就体现在 A 协程暂停执行，B 协程恢复执行；同样，也可以从 B 协程中启动 A 协程。通常，如果从 A 协程启动 B 协程，我们就把 A 协程称为 B 协程的**父协程**。

正如一个进程可以拥有多个线程一样，一个线程也可以拥有多个协程。最重要的是，协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行）。这样带来的好处就是性能得到了很大的提升，不会像线程切换那样消耗资源。

当使用 yield 暂停协程的时候，父协程就会开始执行，也就是调用Generator的进程。

### async/await

async 是一个通过**异步执行**并**隐式返回** Promise作为结果的函数。

```js
async function foo() {
    return 2
}
console.log(foo());
// foo() 返回的是  Promise {<resolved>: 2}

```

至于 await，看下面代码：

```js
async function foo() {
    console.log(1)
    let a = await 100
    console.log(a)
    console.log(2)
}
console.log(0)
foo()
console.log(3)

```

首先，执行`console.log(0)`这个语句，打印出来 0。

紧接着就是执行 foo 函数，由于 foo 函数是被 async 标记过的，所以当进入该函数的时候，首先执行 foo 函数中的`console.log(1)`语句，并打印出 1。

接下来就执行到 foo 函数中的`await 100`这个语句了，当执行到`await 100`时，会默认创建一个 Promise 对象，代码如下所示：

```js
let promise_ = new Promise((resolve,reject){
  resolve(100)
})

```

此时由于resolve(100)本质上是个异步操作，所以会加入到微任务列表中。

程序的控制交给父协程了，此时打印`console.log(3)`.

随后父协程将执行结束，在结束之前，会进入微任务的检查点，然后执行微任务队列，微任务队列中有`resolve(100)`的任务等待执行，执行到这里的时候，会触发 `promise_.then` 中的回调函数，如下所示：

```js
promise_.then((value)=>{
   // 回调函数被激活后
  // 将主线程控制权交给 foo 协程，并将 vaule 值传给协程
})

```

foo 协程激活之后，会把刚才的 value 值赋给了变量 a，然后 foo 协程继续执行后续语句，执行完成之后，将控制权归还给父协程。于是打印100和2.

所以最终的打印顺序：0，1，3，100，2。





## 思考题

下面代码输出什么？

```js
async function foo() {
    console.log('foo');
}
async function bar() {
    console.log('bar start');
    await foo();
    console.log('bar end');
}
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0);
bar();
new Promise(function(resolve) {
    console.log('promise executor');
    resolve();
}).then(function() {
    console.log('promise then');
});
console.log('script end');
```

分析：

1. 首先在主协程中初始化异步函数foo和bar，碰到console.log打印script start；
2. 解析到setTimeout，初始化一个Timer，创建一个新的task
3. 执行bar函数，将控制权交给协程，输出bar start，碰到await，执行foo，输出foo，创建一个 Promise返回给主协程
4. 将返回的promise添加到微任务队列，向下执行 new Promise，输出 promise executor，返回resolve 添加到微任务队列
5. 输出script end
6. 当前task结束之前检查微任务队列，执行第一个微任务，将控制器交给协程输出bar end
7. 执行第二个微任务 输出 promise then
8. 当前任务执行完毕进入下一个任务，输出setTimeout









