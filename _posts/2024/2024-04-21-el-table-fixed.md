---
layout: mypost
title: el-table设置fixed列后，可能导致样式错位问题
tags:
  - ElementUI
---

如题，一旦设置 table 中的某些列为 fixed ，可能会导致数据更新后表格显示错位的 bug。

doLayout（官方提供）

```js
this.$nextTick(() => {
  this.$refs.tableRef.doLayout()
})
```

> 另外，有人提到，使用this.$refs.table.doLayout()依然不能解决问题？

回答：只需要给你的列设定至少一个或一个以上的min-width就解决了，全部都是设置的width并且超出了列表的宽度，就会出现这个问题。
