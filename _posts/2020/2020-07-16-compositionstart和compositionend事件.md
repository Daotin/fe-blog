---
title: compositionstart和compositionend事件
tags: JavaScript
---

1. 目录
{:toc}

## 需求

最近有个需求，根据input输入的文字进行列表过滤。这是个很常见的需求。于是大致的代码如下：

<!--more-->

```html
<template>
  <div id="app">
    <input type="text" :value="filterText" @input="onInput" />
    <ul>
      <li v-for="item in filteredList" :key="item">
        {{ item }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      filterText: "",
      list: [
        "爱与希望",
        "花海",
        "Mojito",
        "最长的电影",
        "爷爷泡的茶"
      ]
    };
  },
  computed: {
    filteredList() {
      if (!this.filterText) {
        return this.list;
      }
      return this.list.filter(item => item.indexOf(this.filterText) > -1);
    }
  },
  methods: {
    onInput(e) {
      this.filterText = e.target.value;
    }
  }
};
</script>
```

在输入框中监听input事件，然后触发filteredList列表的改变。

一切都是那么自然。

然而当我们输入中文的时候，由于拼音会先显示，导致在输入中文的过程中，触发筛选的列表空的，最后中文显示出来的时候，才会有显示结果。

## compositionstart和compositionend

于是在网上搜索有这么两个事件，`compositionstart`和`compositionend`

> MDN: https://developer.mozilla.org/zh-CN/docs/Web/Events/compositionstart

**当用户使用拼音输入法开始输入汉字时，compositionstart事件就会被触发。当文本段落的组成完成或取消时, compositionend 事件将被触发。**

也就是说，在我们开始输入中文的时候会触发一次compositionstart事件，中文输入过程中不会再出发compositionstart事件，最后输入中文完成触发compositionend 事件。

> 而且经过试验发现，在输入中文的时候，compositionstart先于input事件触发。

有了这个前提那这就好办了，我只需打个标`lock`，当compositionstart触发时，`lock=true`，当compositionend触发时，`lock=false`。只有在lock为false的时候，才执行input事件中的筛选操作。


代码变成如下：

```html
<template>
  <div id="app">
    <input type="text" :value="filterText" 
        @input="onInput" 
        @compositionstart="onCompositionStart"
        @compositionend="onCompositionEnd"
        />
    <ul>
      <li v-for="item in filteredList" :key="item">
        {{ item }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      filterText: "",
      list: [
        "爱与希望",
        "花海",
        "Mojito",
        "最长的电影",
        "爷爷泡的茶"
      ]，
      lock; false, // 打标
    };
  },
  computed: {
    filteredList() {
      if (!this.filterText) {
        return this.list;
      }
      return this.list.filter(item => item.indexOf(this.filterText) > -1);
    }
  },
  methods: {
    onInput(e) {
      if (!this.lock) {
        this.filterText = e.target.value;
      }
    }，
    onCompositionStart() {
      this.lock = true;
    },
    onCompositionEnd(e) {
      this.filterText = e.data;
      this.lock = false;
    }
  }
};
</script>
```

## v-model形式

上面的代码我们使用的不是vue的`v-model`双向绑定的形式，如果你使用`v-model`的形式，你会发现在输入中文的过程中不会触发input事件。

查看vue的源码`src/platforms/web/runtime/directives/model.js`，有这么几行代码：

```javascript
export default {
  inserted (el, binding, vnode) {
    if (vnode.tag === 'select') {
      setSelected(el, binding, vnode.context)
      el._vOptions = [].map.call(el.options, getValue)
    } else if (vnode.tag === 'textarea' || isTextInputType(el.type)) {
      el._vModifiers = binding.modifiers
      if (!binding.modifiers.lazy) {
        // Safari < 10.2 & UIWebView doesn't fire compositionend when
        // switching focus before confirming composition choice
        // this also fixes the issue where some browsers e.g. iOS Chrome
        // fires "change" instead of "input" on autocomplete.
        el.addEventListener('change', onCompositionEnd)
        if (!isAndroid) {
          el.addEventListener('compositionstart', onCompositionStart)
          el.addEventListener('compositionend', onCompositionEnd)
        }
        /* istanbul ignore if */
        if (isIE9) {
          el.vmodel = true
        }
      }
    }
  }
}

//...

function onCompositionStart (e) {
  e.target.composing = true
}

function onCompositionEnd (e) {
  // prevent triggering an input event for no reason
  if (!e.target.composing) return
  e.target.composing = false
  trigger(e.target, 'input')
}

function trigger (el, type) {
  const e = document.createEvent('HTMLEvents')
  e.initEvent(type, true, true)
  el.dispatchEvent(e)
}
```

可以发现，原来vue早已做了相同的操作，所以v-model帮我们做了很多优化处理，这也是vue如此优秀的原因之一。



（完）

