---
title: vue用template还是JSX
tags: vue jsx
---

1. 目录
{:toc}


## 各自特点
template
- 模板语法（HTML的扩展）
- 数据绑定使用Mustache语法（双大括号）
```html
<span>{{title}}<span>
```

<!--more-->

JSX
- JavaScript的语法扩展
- 数据绑定使用单引号
```jsx
<span>{title}<span>
```

## vue官方建议

Vue官方建议使用`template`模板，但是 ：
> “更抽象一点来看，我们可以把组件区分为两类：一类是偏视图表现的 (presentational)，一类则是偏逻辑的 (logical)。我们推荐在前者中使用模板，在后者中使用 JSX 或渲染函数。这两类组件的比例会根据应用类型的不同有所变化，但整体来说我们发现表现类的组件远远多于逻辑类组件。”

也就是说，在一些特定场景下可以建议使用JSX语法。


## JSX语法如何在vue中使用

先看下template的情况
```vue
<!--nav-tmpl.vue-->
<template>
  <h1 v-if="level === 1">
    <slot></slot>
  </h1>
  <h2 v-else-if="level === 2">
    <slot></slot>
  </h2>
  <h3 v-else-if="level === 3">
    <slot></slot>
  </h3>
  <h4 v-else-if="level === 4">
    <slot></slot>
  </h4>
  <h5 v-else-if="level === 5">
    <slot></slot>
  </h5>
  <h6 v-else-if="level === 6">
    <slot></slot>
  </h6>
</template>
<script>
export default {
  props: {
    level: {
      type: Number,
      default: 1
    }
  }
};
</script>

```

是不是很多v-if-else 看的眼花缭乱？

别着急，来看jsx大法。

```jsx
// nav-jsx.jsx
export default {
  props: {
    level: {
      type: Number,
      default: 1
    }
  },
  render: function(h) {
    const Tag = `h${this.level}`;
    return <Tag>{this.$slots.default}</Tag>;
  }
};

```

是不是简洁了许多？！


jsx组件使用的方式和vue组件相同，先导入，然后components注册，就可以使用了。

```vue
<template>
  <div class="demo">
    <Nav :level="2">标题</Nav>
    <navjsx :level="1">标题</navjsx>
  </div>
</template>

<script>
import Nav from "@/components/Nav.vue";
import navjsx from "@/components/nav.jsx";
export default {
  components: { Nav,navjsx },
  data() {
    return {}
  }
};
```

## template和jsx混用

我们也可以混用template和jsx语法。通过在components中注册一个函数式组件，渲染jsx语法的标签。

```vue
<template>
  <div class="demo">
    <Nav :level="2">{{this.title}}</Nav>
    <navjsx :level="1">{{this.title}}</navjsx>
    <VNodes :vnodes="showJSX(1)" />
  </div>
</template>

<script>
import Button from "@/components/Button.vue";
import Nav from "@/components/Nav.vue";
import navjsx from "@/components/nav.jsx";
export default {
  components: {
    Button,Nav,navjsx,
    VNodes: {
      functional: true,
      render: (h, ctx) => ctx.props.vnodes
    }
  },
  data() {
    return {
      title: "标题",
    };
  },
  methods: {
      showJSX(level) {
          const Tag = `h${level}`;
          return <Tag>{this.title}</Tag>;
      }
  },
</script>
```



（完）


