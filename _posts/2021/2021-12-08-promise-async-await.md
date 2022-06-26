---
layout: mypost
title: promise,async和await
tags: xxx
---

1. 目录
{:toc}

<!--more-->

制作一个定时器，2s过后，获取一个字符串，然后在控制台输出这个字符串，初始代码如下。

![](/image/5.png)

1. 一个最容易想到的解决办法就是把输出语句放到setTimeout里面，这样做是绝对正确的，但是如果异步操作有很多，就会出现层层嵌套的问题。
2. 当然，我们也可以使用Proxy代理观察gift值的变化。

![](/image/6.png)



3. 使用promise

promise对象在创建的时候分别接收了2个内部的函数钩子：resolve（已完成）和reject（已拒绝），promise对象就是一种承诺，在必要的时候，它会告知外部本次异步操作已经完成或者拒绝，如果是完成，则触发后面的then方法；如果是拒绝，则触发catch方法。

![](/image/7.png)


看到这里，可能有的人又会纳闷，虽然用promise对象处理起来更加优雅，但是我们不是还要在对应的then方法或者catch方法里面进行操作吗？能不能直接给我resolve里面的值，不要逼着我去then里面处理数据？办法是有的，这就需要配合async函数和await关键字了。

![](/image/8.png)



之前，代码的困境是无法脱离then和catch的回调函数，导致代码还是有些冗余，其实我们只是希望得到resolve里面的参数而已，下面简单介绍上面的代码。

getGiftAsync函数返回了一个promise对象，逻辑和刚才一样，然后在executeAsyncFunc函数的左边加上了async，代表这是一个异步处理函数。只有加上了async关键字的函数，内部才可以使用await关键字。async是ES7才提供的与异步操作有关的关键字，**async函数执行时，一旦遇到await就会先暂停执行，等到触发的异步操作完成后，才会恢复async函数的执行并返回解析值。**

当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体后面的语句。



## async函数

### 描述：

await表达式会暂停整个async函数的执行进程并出让其控制权，只有当其等待的**基于promise的异步操作被兑现或被拒绝之后才会恢复进程**。（也就是说使用setTimeout的异步，不会暂停async函数的执行）



```ts
function resolveAfter2Seconds() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
      console.log('==>', 2)
    }, 2000);
  });
}

async function foo() {
  console.log('==>', 1)
  await resolveAfter2Seconds();
  console.log('==>', 3)
}


console.log('foo==>', foo())

// 打印结果：先打印1,2行，再一起打印3,4行
==> 1
foo==> Promise { <pending> }
==> 2
==> 3

```

打印结果：先打印1,2行，再一起打印3,4行。



如果使用setTimeout的话：

```ts
async function foo() {
  console.log('==>', 1)
  await setTimeout(() => {
    console.log('==>', 2)
   }, 2000);
  console.log('==>', 3)
}

console.log('foo==>', foo())

// 打印结果
==> 1
foo==> Promise { <pending> }
==> 3
==> 2

```

打印结果：先打印1，2，3行，再打印第4行。



### 返回值：

一个Promise，这个promise要么会通过一个由async函数返回的值被解决，要么会通过一个从async函数中抛出的（或其中没有被捕获到的）异常被拒绝。

promise的解决值（resolve的值）会被当作该await表达式的返回值。

**async函数一定会返回一个promise对象。**如果一个async函数的返回值看起来不是promise，那么它将会被隐式地包装在一个promise中。



例如：

```ts
async function foo() {
   return 1
}
// 等价于
function foo() {
   return Promise.resolve(1)
}

```

也就是：async函数内部return语句返回的值，会成为then方法回调函数的参数。

```ts
async function f(){
  return 'hello world';
}
f().then(v=>console.log(v))
// "hello world;"
```

### 执行过程

**一个不含await表达式的async函数是会同步运行的（就像是一个普通的函数）。**然而，如果函数体内有一个await表达式，async函数就一定会异步执行。

```ts
async function foo() {
   await 1
}
// 等价于
function foo() {
   return Promise.resolve(1).then(() => undefined)
}

```

在await表达式之后的代码可以被认为是存在在链式调用的then回调中，多个await表达式都将加入链式调用的then回调中，返回值将作为最后一个then回调的返回值。


## promise的then、catch、finally

示例代码：

```js
let p = new Promise((resolve, reject) => {
  resolve('success')
  // reject('fail')
})

p.then(res => {
  console.log('resolve==>', res)
  // setTimeout(() => {
  //   throw 'err1'
  // }, 10);
  throw 'err1'
}, err => {
  console.log('reject==>', err)
})
.catch(err => {
  console.log('catch==>', err)
})
.finally(_ => {
  console.log('==>finally')
})
```



结论：

- 当new promise抛出异常（不管是使用reject还是throw）时：
    - 如果then的第二个参数写了，则不会走catch，否则会走catch
    - 在then的第一个参数里面可以抛出异常，catch可以捕获到；但是如果是在异步操作中抛出的异常，则catch无法捕获
- 当new promise抛出异常如果是异步的，则会报错。
- finally无论成功与否都会执行。


## promise中resolve和return resolve的区别

下面二者有啥区别？

```ts
return new Promise( (resolve, reject) => {
  fs.readFile(file, (err, data) => {
    if (err) reject(err)
    return resolve(data)
  })
})

return new Promise( (resolve, reject) => {
  fs.readFile(file, (err, data) => {
    if (err) reject(err)
    resolve(data)
  })
})

```



解答：

return resolve() 将像一个正常的返回一样结束函数的执行，这只是取决于你的代码流程，如果你不希望或需要在你的函数中执行任何更多的代码，那么使用return来退出函数，仅此而已。不会改变返回的值。

```ts
return new Promise( (resolve, reject) => {
  fs.readFile(file, (err, data) => {
    if (err) reject(err)
    return resolve(data)
    console.log('after return') // 不会执行
  })
})
```

只有resolve会创建一个成功的承诺状态，return不会。

这与函数中的普通return语句相同，与promise无关。



参考链接：

[https://stackoverflow.com/questions/42349338/promise-what-is-the-difference-between-return-resolve-and-resolve#](https://stackoverflow.com/questions/42349338/promise-what-is-the-difference-between-return-resolve-and-resolve#)

（完）

