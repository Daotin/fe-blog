---
title: vue的provide和inject特性
tags: Vue
---

1. 目录
{:toc}


## 由来

组件之间的通信可以通过props和$emit的方式进行通信，但是如果组件之间的关系非常复杂的话，通过以上的方式会很麻烦，并且程序会非常脆弱，没有建中性可言。

在**vue2.2.0 中新增provide和inject属性**，可以方便的帮助我们进行组件间的传值。

使用的方式很简单：
**父组件通过provide提供数据，其他组价可以使用inject注入数据。**

<!--more-->

## 注意

**不推荐直接用于应用程序代码中。一般使用的场景是自定义组件库的时候，底层组件之间需要通信的时候使用。**

> provide 和 inject 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。**业务开发可以优先考虑vuex提供全局唯一store**， provide inject 会有一个类似冒泡的特性，数据源有可能在中间被”“打断”（比如A的子组件为B，B的子组件为C，当A提供provide的时候，C可以inject到，但是当B也有provide相同属性的时候，A传递给C的就会被B的provide打断，被截胡了。。。），甚至是有可能被组件库中的组件打断，或者打断组件库中的provide，不利于维护。

## 特点

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

## 格式

**provide** 选项应该是 **一个对象或返回一个对象的函数。** 该对象包含可注入其子孙的属性。

**inject** 选项应该是：
- `一个字符串数组`
- 或 `一个对象`，对象的 `key` 一般为provide传递的对象的属性名（_当然也可以改名字，当使用自定义名称的时候，需要使用 from 来表示其源属性：_），`value` 是一个对象，该对象的：
    - `from` 属性是provide传过来的源属性;
    - `default` ：与 prop 的默认值类似，你需要对非原始值使用一个工厂方法：`default: () => {}`

> 提示：provide 和 inject 绑定并不是可响应的。其中一个办法是你要下发给子组件的是父组件的this。
> **PS：Vue 2.6之后可以通过`vue.observe`按需传递响应式数据。**


```js
  provide() {
    return {
      parent: this
    };
```


## 示例：
父组件

```html
<template>
  <div>
    <h1>HelloWorld</h1>
    <One></One>
  </div>
</template>

<script>
import One from "./One";
export default {
  components: { One },
  // provide: {
  //   for: "这是父组件的provide"
  // }
  provide() {
    return {
      for: "这是父组件的provide"
    };
  }
};
</script>
```

子组件1：

```html
<template>
  <div>
    <h2>childOne组件</h2>
    {{demo}}
    <Two></Two>
  </div>
</template>

<script>
import Two from "./Two.vue";
export default {
  name: "One",
  // inject: ["for"],
  inject: {
    for: {
      default: () => ({})
    }
  },
  data() {
    return {
      demo: this.for
    };
  },
  components: {
    Two
  }
};
</script>
```


子组件2：

```html
<template>
  <div>
    <h2>childtwo组件</h2>
    {{demo}}
  </div>
</template>

<script>
export default {
  name: "Two",
  // inject: ["for"],
  inject: {
    for1: {
      from:'for',
      default: () => ({})
    }
  },
  data() {
    return {
      demo: this.for1
      // demo: "childTwo"
    };
  }
};
</script>
```

此时，两个子组件均会收到父组件传递的数据：
![](https://img-blog.csdnimg.cn/20190407173313888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x2b252ZQ==,size_16,color_FFFFFF,t_70)




（完）

