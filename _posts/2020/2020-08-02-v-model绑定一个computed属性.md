---
title: 如何使用 v-model 绑定一个 computed 属性？
tags: Vue
---

1. 目录
{:toc}


## 由来
当使用 `v-model` 去一个`computed`属性，然后修改这个`computed`属性的时候，就会报错。

![image](https://user-images.githubusercontent.com/23518990/90099160-68a3e780-dd6c-11ea-8eed-09e0d0d4c397.png)

<!--more-->

## 解决方法
1、用“Vuex 的思维”去解决这个问题。给 `<input>` 中绑定 value，然后侦听 input 或者 change 事件，在事件回调中调用一个方法。

```js
<input :value="message" @input="updateMessage">
  
computed: {
  message() {
    return this.msg + '%';
  }
},
methods: {
  updateMessage (e) {
    this.msg = e.target.value;
  }
}
```


2、使用带有 `setter` 的双向绑定计算属性:

```js
<input v-model="message">

computed: {
  message: {
    get () {
      return this.msg + '%';
    },
    set (value) {
      this.msg = value;
    }
  }
}
```

## 举例
比如：**购物车的全选按钮使用的是其他单选按钮遍历得到的是否全选状态。**

全选的部分不使用 v-model：

```html
<div class="select-all">
    <input type="checkbox" id="fb" :checked="allCheck" @change="setAllCheck">
    <label for="fb">全选</label>
</div>
```
- 单选改变--->computed ---> allCheck改变
- 点击allCheck的input，执行函数setAllCheck
- setAllCheck里面，把所有的单选全部选中/取消，进而通过第一步对allCheck进行~~曲线救国~~改变。



（完）

