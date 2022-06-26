---
title: js能够保证object属性的输出顺序吗？
tags: JavaScript
---

- [http://jartto.wang/2016/10/25/does-js-guarantee-object-property-order/](http://jartto.wang/2016/10/25/does-js-guarantee-object-property-order/)
- [https://blog.liangzr.tech/2019/03/09/how-to-print-properties-of-the-object-by-originally/](https://blog.liangzr.tech/2019/03/09/how-to-print-properties-of-the-object-by-originally/)
- [https://www.geekjc.com/post/5b0cdf46dbec6c731e99a196](https://www.geekjc.com/post/5b0cdf46dbec6c731e99a196)



使用ES6的新特性Map。Map 对象以插入的顺序遍历元素。for...of循环为每一次循环返回一个[key, value]数组。

一个Map对象在迭代时会根据对象中元素的插入顺序来进行 — 一个 for...of 循环在每次迭代后会返回一个形式为[key，value]的数组。

```js
var myObject = new Map();
myObject.set('z', 1);
myObject.set('@', 2);
myObject.set('b', 3);
for (var [key, value] of myObject) {
  console.log(key, value);
...
// z 1
// @ 2
// b 3

```



如果过你想在跨浏览器环境中模拟一个有序的关联数组，你要么使用两个分开的数组（一个保存key，另一个保存value）,要么构建一个单属性对象(single-property objects)的数组。

```js
// 使用分开的数组
var objectKeys = [z, @, b, 1, 5];
for (item in objectKeys) {
  myObject[item]
...

// 构建一个单属性对象(single-property objects)的数组
var myData = [{z: 1}, {'@': 2}, {b: 3}, {1: 4}, {5: 5}];
```

