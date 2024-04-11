---
layout: mypost
title: vue2,vue3深度选择器使用区别
tags:
  - vue
---


在vue2中可以使用`::v-deep`, `>>>`, 和 `/deep/`，使用方式相同：

```css
<style scoped>
::v-deep .nested-component {
  /* 样式会影响到所有嵌套的 .nested-component 类 */
}
</style>
```

Vue 3 中推荐使用 `::v-deep`，而 `>>>` 和 `/deep/` 不再推荐使用，并可能在未来的版本中被废弃。

```css
/* Vue 3 推荐用法 */
<style scoped>
::v-deep(.nested-component) {
  /* 样式 */
}
</style>

```

> 另外， `::v-deep` 是否需要前置类（如 `.aaa ::v-deep ...`）？

不需要，可以加可以不加。如上面代码展示的，就没有前置类。

加了前置类的如下：

```css
<style scoped>
.aaa ::v-deep .nested-component {
  /* 只有在 .aaa 内部的 .nested-component 类将受到影响 */
}
</style>

```


