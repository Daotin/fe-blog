调用栈是一种用来管理执行上下文的数据结构，符合后进先出的规则。不过还有一点你要注意，**调用栈是有大小的**，当入栈的执行上下文超过一定数目，JavaScript 引擎就会报错，我们把这种错误叫做**栈溢出**。



下面是两段代码：

```js
let myname= '极客时间'
{
    console.log(myname)  // 极客时间
    // let myname= '极客邦'
}
```

```js
let myname= '极客时间'
{
    console.log(myname)  // Uncaught ReferenceError: Cannot access 'myname' before initialization
    let myname= '极客邦'
}
```

