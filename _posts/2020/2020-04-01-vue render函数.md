---
title: vue render函数
tags: vue
---

1. 目录
{:toc}



## 为什么使用render函数代替template模板？

Vue 推荐在绝大多数情况下使用模板来创建你的 HTML。然而在一些场景中，你真的需要 JavaScript 的完全编程的能力。这时你可以用渲染函数，它比模板更接近编译器。

> 因为vue是虚拟DOM，所以在拿到template模板时也要转译成VNode的函数，而用render函数构建DOM，vue就免去了转译的过程。

我们来看一个官网的例子：*分别使用template语法和render函数来实现根据传入的 level （h1-h6）页面渲染不同的标题格式。*

<!--more-->


在子组件中是这样的：

```html
// 子组件 child1.vue
<template>
  <div>
    <h1 v-if="level == 1">
      <slot></slot>
    </h1>
    <h2 v-if="level == 2">
      <slot></slot>
    </h2>
    <h3 v-if="level == 3">
      <slot></slot>
    </h3>
    <h4 v-if="level == 4">
      <slot></slot>
    </h4>
    <h5 v-if="level == 5">
      <slot></slot>
    </h5>
    <h6 v-if="level == 6">
      <slot></slot>
    </h6>
  </div>
</template>
<script>
  export default {
    props: {
      level: {
        require: true,
        type: Number,
      }
    }
  };
</script>

```

然后父组件调用：

```html
<anchored-heading :level="1">Hello world!</anchored-heading>
```

看起来特别冗杂，我们看看render的实现：

```html
<script>
  export default {
    props: {
      level: {
        require: true,
        type: Number,
      }
    },
    render(createElement) {
      return createElement('h' + this.level, this.$slots.default);
    }
  };
</script>
```

对比两种实现方式，我们发现这里用模板并不是最好的选择，不但代码冗长，而且在每一个级别的标题中重复书写了<slot></slot>。 使用render函数实现看起来简单多了，这样代码精简很多，但是需要非常熟悉 Vue 的实例属性（`default` 属性包括了所有没有被包含在具名插槽中的节点，或 `v-slot:default` 的内容。）。

## render函数
render 函数即渲染函数，它是个函数，render 函数的返回值是VNode（即：虚拟节点，也就是我们要渲染的节点）

createElement 是 render 函数的参数，它本身也是个函数），并且有三个参数。接下来点介绍这三个参数。

> **CreateElement函数在惯例中通常也写作h**。

### 第一个参数(必须) ： {String | Object | Function}

- `String`，表示的是HTML 标签名，如`div`
- `Object` ，一个含有数据的组件选项对象
- `Function` ，返回了一个含有标签名或者组件选项对象的async 函数

```js
render: function (createElement) {
  // String
  return createElement('h1');

  //Object
  return createElement({
    template: " <div>道廷途说</div> "
  })

  // Function
  let domFun = function () {
    return {
      template: " <div>道廷途说</div> "
    }
  }
  return createElement(domFun())
}
```

### 第二个参数(可选) - {Object}
一个与模板中属性对应的数据对象 常用的有class | style | attrs | domProps | on

- class：控制类名
- style ：样式
- attrs ：用来写正常的 html 属性 id src 等等
- domProps :用来写原生的dom 属性
- on:：用来写原生方法

```js
return createElement('div', {
  // 和`v-bind:class`一样的 API
  'class': {
    foo: true,
    bar: false
  },
  // 和`v-bind:style`一样的 API
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 正常的 HTML 特性
  attrs: {
    id: 'foo'
  },
  // 组件 props
  props: {
    myProp: 'bar'
  },
  // DOM 属性
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器基于 "on"
  // 所以不再支持如 v-on:keyup.enter 修饰器
  // 需要手动匹配 keyCode。
  on: {
    click: this.clickHandler
  },
  // 仅对于组件，用于监听原生事件，而不是组件使用 vm.$emit 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler
  },
  // 自定义指令. 注意事项：不能对绑定的旧值设值
  // Vue 会为您持续追踨
  directives: [
    {
      name: 'my-custom-directive',
      value: '2'
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // Scoped slots in the form of
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => h('span', props.text)
  },
  // 如果子组件有定义 slot 的名称
  slot: 'name-of-slot'
  // 其他特殊顶层属性
  key: 'myKey',
  ref: 'myRef'
})
```

### 第三个参数(可选) - {String | Array}
代表子级虚拟节点 (VNodes)，由 `createElement()` 构建而成（*参数同上面参数一二*），正常来讲接收的是一个字符串或者一个数组，一般数组用的是比较多的

```js
  return createElement('div', {
    //...
  }, [
      createElement('h1', '我是H1标题'),
      createElement('h6', '我是H6标题')
    ]
  )
```


## 事件修饰符

```js
on: {
  '!click': this.doThisInCapturingMode,
  '~keyup': this.doThisOnce,
  '~!mouseover': this.doThisOnceInCapturingMode
}

```

template事件修饰符 | render写法前缀
---|---
`.passive` | 	`&`
`.capture` | 	`!`
`.once` | 	`~`
`.capture.once 或.once.capture` | 	`~!`



## render中的JSX配置和用法

写了这么多createElement，有的写起来也挺麻烦的，特别是还有标签嵌套的情况下更是复杂。我们可以使用JSX语法方便我们书写render函数，不过在写之前需要一点配置。

### 配置

- 安装vue对JSX的依赖
```txt
npm install\
  babel-plugin-syntax-jsx\
  babel-plugin-transform-vue-jsx\
  babel-helper-vue-jsx-merge-props\
  babel-preset-env\
  --save-dev
```

- 安装完成以后，在`.babelrc`文件配置 `"plugins": ["transform-vue-jsx"]`。
```
{
  "presets": ["env"],
  "plugins": ["transform-vue-jsx"]
}
```

这个插件可以将：

```html
<div id="foo">{this.text}</div>
```

转化成如下的形式：

```js
h('div', {
  attrs: {
    id: 'foo'
  }
}, [this.text])
```


- ~~将webpack配置文件中的js解析部分改成test: /\.jsx?$/表示对jsx的代码块进行解析。~~

### 使用

改用jsx之前我们这样写：

```js
createElement(
  'anchored-heading', {
    props: {
      level: 1
    }
  }, [
    createElement('span', 'Hello'),
    ' world!'
  ]
)
```

改成JSX之后，我们的render函数可以这样写：

```js
import AnchoredHeading from './AnchoredHeading.vue'

new Vue({
  el: '#demo',
  render (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```

### 注意事项

> 注意：h 函数是 Vue 实例的 $createElement 方法的简写，它必须位于 JSX 所在的范围内。 因为这个方法是作为第一个参数传递给组件渲染函数的，所以在大多数情况下你会这样做:

```js
Vue.component('jsx-example', {
  render (h) { // <-- h must be in scope
    return <div id="foo">bar</div>
  }
})
```
然而，从3.4.0版本开始，会在每种方法中自动注入 `const h = this.$createElement`，这样你就可以去掉(h)参数了。


## 参考文档
- https://juejin.im/post/5d5b4379518825637965eb6a
- https://github.com/vuejs/babel-plugin-transform-vue-jsx


（完）

