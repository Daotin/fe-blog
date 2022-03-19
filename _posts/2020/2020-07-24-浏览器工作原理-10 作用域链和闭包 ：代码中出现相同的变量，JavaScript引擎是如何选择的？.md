下面代码打印什么？

```js
function bar() {
    console.log(myName)
}
function foo() {
    var myName = " 极客邦 "
    bar()
}
var myName = " 极客时间 "
foo()
```

答案是：极客时间。而不是极客帮。



## 作用域链

从作用域链的角度出发，bar里面没有 myName 所以会往上找，但是->

*为什么 bar 函数的外部引用是全局执行上下文，而不是 foo 函数的执行上下文？*

## 词法作用域

词法作用域就是指作用域是由代码中**函数声明的位置**来决定的，所以词法作用域是静态的作用域。和函数是怎么调用的没有关系。

*那么，根据作用域链 + 词法作用域 下面的代码输出什么？*

```js
function bar() {
    var myName = ' 极客世界 ';
    let test1 = 100;
    if (1) {
        let myName = 'Chrome 浏览器 ';
        console.log(test);
    }
}
function foo() {
    var myName = ' 极客邦 ';
    let test = 2;
    {
        let test = 3;
        bar();
    }
}
var myName = ' 极客时间 ';
let myAge = 10;
let test = 1;
foo(); // 答案是：1
```



## 闭包

在 JavaScript 中，根据词法作用域的规则，内部函数总是可以访问其外部函数中声明的变量，当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束了，但是内部函数引用外部函数的变量依然保存在内存中，我们就把**这些变量的集合称为闭包**。比如外部函数是 foo，那么这些变量的集合就称为 foo 函数的闭包



你也可以通过“开发者工具”来看看闭包的情况，打开 Chrome 的“开发者工具”，在 bar 函数任意地方打上断点，然后刷新页面，可以看到如下内容：

![](http://img.vim-cn.com/e7/e0f7b4ba1578f05f7d367e9af63e9a11407595.png)



从图中可以看出来，当调用 bar.getName 的时候，右边 Scope 项就体现出了作用域链的情况：Local 就是当前的 getName 函数的作用域，Closure(foo) 是指 foo 函数的闭包，最下面的 Global 就是指全局作用域，从“Local–>Closure(foo)–>Global”就是一个完整的作用域链。



### 闭包的垃圾回收

通常，如果引用闭包的函数是一个全局变量，那么闭包会一直存在直到页面关闭；但如果这个闭包以后不再使用的话，就会造成内存泄漏。

如果引用闭包的函数是个局部变量，等函数销毁后，在下次 JavaScript 引擎执行垃圾回收时，判断闭包这块内容如果已经不再被使用了，那么 JavaScript 引擎的垃圾回收器就会回收这块内存。

所以，**如果该闭包会一直使用，那么它可以作为全局变量而存在；但如果使用频率不高，而且占用内存又比较大的话，那就尽量让它成为一个局部变量。**



## 思考题

下面代码打印什么？

```js
var bar = {
    myName: 'time.geekbang.com',
    printName: function() {
        console.log(myName);
    },
};
function foo() {
    let myName = ' 极客时间 ';
    return bar.printName;
}
let myName = ' 极客邦 ';
let _printName = foo();
_printName(); // ?
bar.printName(); // 极客邦
```

我知道bar.printName(); 打印的是极客邦，以为_printName();打印的是极客时间，然而结果还是极客邦。

**分析：**

相信你已经知道了，在 printName 函数里面使用的变量 myName 是属于全局作用域下面的，所以最终打印出来的值都是“极客邦”。这是因为 JavaScript 语言的作用域链是由词法作用域决定的，而词法作用域是由代码结构来确定的。

不过按照常理来说，调用bar.printName方法时，该方法内部的变量 myName 应该使用 bar 对象中的，因为它们是一个整体。但是 JavaScript 的作用域机制并不支持这一点，基于这个需求，JavaScript 又搞出来了 this。

- bar 不是一个函数，因此 bar 当中的 printName 其实是一个全局声明的函数，bar 当中的 myName 只是对象的一个属性，也和 printName 没有联系，如果要产生联系，需要使用 this 关键字，表示这里的 myName 是对象的一个属性，不然的话，printName 会通过词法作用域链去到其声明的环境，也就是全局，去找 myName

- foo 函数返回的 printName 是全局声明的函数，因此和 foo 当中定义的变量也没有任何联系，这个时候 foo 函数返回 printName 并不会产生闭包



如果改成下面的方式：

```js
var bar = function() {
    let printName = function() {
        console.log(myName);
    }
    return printName;
};

function foo() {
    let myName = ' 极客时间 ';
    return bar;
}

let myName = ' 极客邦 ';
let _printName = foo();

_printName()();
bar()();
```

答案还都是极客邦。



## 总结

js中同名变量如何取值？

**首先确定词法环境，是取得局部变量还是全局变量。（词法环境）**

**范围搞清楚了之后，再到此范围确定变量提升的问题。（作用域链）**



往外找是因为你知道作用域链，往哪个外面找是因为词法环境。

![](http://img.vim-cn.com/af/55b2758993324822a170a8e7eb1abfd533028e.png)

