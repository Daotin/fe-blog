---
layout: mypost
title: el-table设置fixed列后，可能导致样式错位问题（终极方案）
tags:
  - ElementUI
---

如题，一旦设置 table 中的某些列为 fixed ，可能会导致数据更新后表格显示错位的 bug。

![](/image/2024/2024-08-09-14-40-09.png)

**尝试解决方案 1：使用 doLayout**

![alt text](/image/2024/20240809144806.png)

```js
this.$nextTick(() => {
  this.$refs.tableRef.doLayout();
});
```

但是，看文档，貌似是用来当 Table 或其祖先元素由隐藏切换为显示时，可能需要调用此方法，所以可能无效。

**尝试解决方案 2：**

> 另外，有人提到，使用 this.$refs.table.doLayout()依然不能解决问题？

回答：只需要给你的列设定至少一个或一个以上的 min-width 就解决了，全部都是设置的 width 并且超出了列表的宽度，就会出现这个问题。

但是依然不能解决问题。

PS：有时候发现，给这些列都设置一些合适的宽度（包括 fixed 列）后，有可能解决错位的问题，但是不能 100%解决，有的还是不行。。。

**终极方案：设置表格和 fixed 的每一行的行高相同**

查看元素可以发现固定列其实是一个叫做`el-table__fixed-right`的 div 内部的 table 产生的，连数据都跟原 table 相同，不同的是把其他列做了隐藏处理，那么就可以呈现出来固定列的效果。

![alt text](/image/2024/20240809144806.png)

所以，只需要把固定列所在的 table 宽度与 el-table\_\_body-wrapper 内的 table 宽度一致即可。

参考：https://github.com/ElemeFE/element/issues/12078

具体代码：

```js
/**
 * Sets the fixed column alignment for a table.
 *
 * @param {RefObject} tableRef - The reference to the table element.
 * @param {number} pageSize - The number of items per page in the table.
 */
export function setTableFixedColumnAlignment(tableRef, pageSize = 10) {
  const tableDom = this.$refs[tableRef].$el;

  if (!tableDom) {
    console.error('setTableFixedColumnAlignment ==> Table element not found.');
    return;
  }

  for (let i = 0; i < pageSize; i++) {
    // 获取左右两个表格的行
    const leftTableRow = tableDom.querySelectorAll('.el-table__body-wrapper tbody tr')[i];
    const rightTableRow = tableDom.querySelectorAll('.el-table__fixed-body-wrapper tbody tr')[i];

    if (!leftTableRow || !rightTableRow) {
      console.error('setTableFixedColumnAlignment ==> Table row element not found.');
      return;
    }

    // 获取左右两个表格的行高
    var leftTableRowHeight = 0;
    var rightTableRowHeight = 0;

    if (leftTableRow) {
      leftTableRowHeight = leftTableRow.getBoundingClientRect().height;
    }
    if (rightTableRow) {
      rightTableRowHeight = rightTableRow.getBoundingClientRect().height;
    }

    // 取两个表格行高的最大值
    const height = Math.max(leftTableRowHeight, rightTableRowHeight);

    if (height > 0) {
      leftTableRow.style.height = `${height}px`;
      rightTableRow.style.height = `${height}px`;
    }
  }
}
```

然后将方法绑定到 Vue 原型链上（这样就不必传入 this 了）：

```js
import { setTableFixedColumnAlignment } from '@/utils/index';

Vue.prototype.setTableFixedColumnAlignment = setTableFixedColumnAlignment;
```

然后在 Vue 文件获取到表格数据后，调用该方法：

```js
data() {
  return {
    queryParams: {
      pageNum: 1,
      pageSize: 10,
      cardMerchantsId: null,
      productType: null,
      chipTypeId: null,
    },
  }
},
methods: {
  getList() {
    this.loading = true
    listVersion(this.queryParams).then(response => {
      this.versionList = response.data.rows
      this.total = response.data.total
      this.loading = false

      // 修复表格多选列对齐问题
      this.$nextTick(() => {
        this.setTableFixedColumnAlignment('tableRef', this.queryParams.pageSize)
      })
    })
  },
}
```

花了一两天时间，此问题终于解决。
