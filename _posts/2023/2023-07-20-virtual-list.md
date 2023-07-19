---
layout: mypost
title: 简析虚拟列表实现
tags: JavaScript
---

## 目录

- [目录](#目录)
- [什么是虚拟列表](#什么是虚拟列表)
- [列表项是固定高度](#列表项是固定高度)
- [列表项是动态高度](#列表项是动态高度)
  - [1. 初始化](#1-初始化)
  - [2. 渲染列表](#2-渲染列表)
  - [3. 调整列表项的高度](#3-调整列表项的高度)
  - [4. 监听滚动事件和初始渲染](#4-监听滚动事件和初始渲染)
- [参考文档](#参考文档)

## 什么是虚拟列表

虚拟列表（也称为窗口滚动、视窗滚动或滚动优化）是一种性能优化技术，主要用于处理大量数据的列表或表格。

当我们在页面上显示成千上万条数据时，如果每一条数据都对应一个 DOM 节点，那么页面的 DOM 节点数量将会非常大，这将对浏览器的渲染性能产生巨大压力，并可能导致浏览器卡顿甚至崩溃。

虚拟滚动的主要思想是只渲染当前视口内的数据，对于视口之外的数据，虽然用户通过滚动条感知到它们的存在，但实际上并没有被渲染出来。当用户滚动列表时，视口内的数据会动态变化，即之前在视口内的数据会被移除，新滚动进视口的数据则会被渲染出来。因此，无论列表有多少数据，实际在 DOM 树中的节点数量都只是视口内的那部分，大大减少了浏览器的渲染压力，提高了性能。

在虚拟列表上的实现上，也分为两种情形：**列表项是固定高度**的和**列表项是动态高度**的。

## 列表项是固定高度

以固定高度为例，实现虚拟滚动主要涉及以下几个关键步骤：

1.  **创建占位元素**：占位元素是一个空的元素，它的高度等于所有列表项的总高度。占位元素的目的是为了在不渲染所有列表项的情况下，保持滚动条的正确性。
    ```javascript
    const placeholder = document.getElementById("placeholder");
    placeholder.style.height = `${dataList.length * ITEM_HEIGHT}px`;
    ```
2.  **计算可见列表项的范围**：通过滚动条的位置和每个列表项的高度，我们可以计算出当前在视口内的列表项的范围（即开始和结束的索引）。
    ```javascript
    let startIndex = Math.max(
      0,
      Math.floor(container.scrollTop / ITEM_HEIGHT) - buffer
    );
    let endIndex = Math.min(
      dataList.length - 1,
      startIndex + Math.ceil(LIST_HEIGHT / ITEM_HEIGHT) + buffer
    );
    ```
3.  **渲染可见列表项**：根据计算出的范围，我们可以渲染出相应的列表项。
    ```javascript
    for (let i = startIndex; i < endIndex; i++) {
      const item = document.createElement("div");
      item.className = "list-item";
      item.innerText = dataList[i];
      content.appendChild(item);
    }
    ```
4.  **更新列表内容的位置**：当滚动条滚动时，我们需要更新列表内容的位置，使其始终保持在视口内。这通常通过修改列表内容的 `transform` 来实现。
    ```javascript
    content.style.transform = `translateY(${startIndex * ITEM_HEIGHT}px)`;
    ```
5.  **监听滚动事件**：当用户滚动列表时，我们需要重新计算可见列表项的范围，并重新渲染列表内容。
    ```javascript
    container.addEventListener("scroll", () => {
      renderList();
    });
    ```

通过以上步骤，虚拟滚动列表能在保持滚动流畅的同时，处理大量数据的列表展示，提供良好的用户体验。此外，为了提高滚动的平滑度，我们还引入了一个缓冲区，即在视口外额外渲染一些列表项，这样当用户快速滚动时，可以避免出现空白的情况。

完整示例代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <!-- 设置响应式布局 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>visual-list</title>
    <style>
      /* 设置列表容器的样式 */
      #list-container {
        width: 200px;
        height: 200px;
        overflow: auto; /* 启用滚动条 */
        border: 1px solid #ccc;
        position: relative; /* 设置为相对定位，使内部的绝对定位元素能相对于它定位 */
      }
      /* 设置列表内容的样式 */
      #content {
        position: absolute; /* 使用绝对定位，使我们能通过改变它的 'top' 来改变它的位置 */
        top: 0;
      }
      /* 设置列表项的样式 */
      .list-item {
        height: 20px;
        line-height: 20px; /* 设置行高使文字垂直居中 */
        margin: 0;
        padding: 0;
      }
    </style>
  </head>
  <body>
    <!-- 创建列表容器 -->
    <div id="list-container">
      <!-- 创建列表内容 -->
      <div id="content"></div>
      <!-- 创建占位元素 -->
      <div id="placeholder"></div>
    </div>
  </body>
  <script>
    /**
     * 初始化数据和参数
     */

    // 列表项的高度
    const ITEM_HEIGHT = 20;
    // 列表的高度
    const LIST_HEIGHT = 200;
    // 数据列表，这里只是模拟数据，实际应用中，会是你需要展示的数据列表
    const dataList = Array.from({ length: 100 }).map((_, i) => `Item ${i}`);

    // 获取占位元素，并设置其高度
    const placeholder = document.getElementById("placeholder");
    placeholder.style.height = `${dataList.length * ITEM_HEIGHT}px`; // 高度等于所有数据项的总高度

    /**
     * 渲染列表
     */
    function renderList() {
      // 输出滚动距离
      console.log("scrollTop==>", container.scrollTop);
      // 缓冲区大小，可以根据需要调整
      const buffer = 5;
      // 计算开始和结束索引
      let startIndex = Math.max(
        0,
        Math.floor(container.scrollTop / ITEM_HEIGHT) - buffer
      );
      let endIndex = Math.min(
        dataList.length - 1,
        startIndex + Math.ceil(LIST_HEIGHT / ITEM_HEIGHT) + buffer
      );

      // 清空列表内容，并设置其位置
      content.innerHTML = "";
      content.style.transform = `translateY(${startIndex * ITEM_HEIGHT}px)`;

      // 输出开始和结束索引
      console.log(startIndex, endIndex);

      // 渲染列表项
      for (let i = startIndex; i < endIndex; i++) {
        const item = document.createElement("div");
        item.className = "list-item";
        item.innerText = dataList[i];
        content.appendChild(item);
      }
    }

    /**
     * 监听滚动事件
     */
    // 获取列表容器和列表内容
    const container = document.getElementById("list-container");
    const content = document.getElementById("content");

    // 监听列表容器的滚动事件，当滚动时，重新渲染列表
    container.addEventListener("scroll", () => {
      renderList();
    });

    // 初始化列表
    renderList();
  </script>
</html>
```

## 列表项是动态高度

如果要实现列表项是动态高度的，设计思路如下：

JavaScript 部分的核心在于`renderList`函数和`adjustItemHeights`函数。

`renderList`函数根据当前滚动条的位置计算出开始和结束的索引，然后根据索引渲染列表项。每次滚动时，都会调用此函数进行重新渲染。

`adjustItemHeights`函数用于调整动态高度的列表项。它会遍历每个新渲染的列表项，获取其实际高度，如果实际高度和预估高度不同，就会更新高度数组和占位元素的高度。

下面是具体的代码分析：

### 1. 初始化

首先，我们定义了一些常量和变量：

```javascript
const LIST_HEIGHT = 200; // 视口的高度
const DEFAULT_ITEM_HEIGHT = 50; // 预估的列表项的高度
const dataList = Array.from({ length: 1000 }).map((_, i) => `Item ${i}`); // 数据源
const itemHeights = Array(dataList.length).fill(DEFAULT_ITEM_HEIGHT); // 初始化每个列表项的高度为预估值
```

`LIST_HEIGHT` 是列表容器的高度，`DEFAULT_ITEM_HEIGHT` 是预估的每个列表项的高度，`dataList` 是数据源，`itemHeights` 是一个数组，用于存储每个列表项的高度，默认为预估的高度。

然后，我们获取`placeholder`元素，并根据预估的高度设置占位元素的高度：

```javascript
const placeholder = document.getElementById("placeholder");
placeholder.style.height = `${itemHeights.reduce((a, b) => a + b, 0)}px`;
```

### 2. 渲染列表

`renderList` 函数是实现虚拟列表的核心。它首先获取当前滚动条的位置，然后通过一个循环来计算出当前可见的列表项的起始和结束索引，然后根据索引渲染列表项。最后，它会调用 `adjustItemHeights` 函数来调整列表项的高度。

```javascript
function renderList() {
  const scrollTop = container.scrollTop; // 获取滚动条的位置
  let startIndex = 0;
  let endIndex = 0;
  let totalHeight = 0;

  // 计算开始和结束的索引
  for (let i = 0; i < itemHeights.length; i++) {
    totalHeight += itemHeights[i];
    if (totalHeight <= scrollTop) {
      startIndex = i;
    }
    if (totalHeight <= scrollTop + LIST_HEIGHT) {
      endIndex = i;
    }
  }

  // 根据索引渲染列表项
  content.style.transform = `translateY(${itemHeights
    .slice(0, startIndex)
    .reduce((a, b) => a + b, 0)}px)`;
  content.innerHTML = "";
  for (let i = startIndex; i <= endIndex; i++) {
    const item = document.createElement("div");
    item.className = "list-item";
    item.innerText = dataList[i];
    content.appendChild(item);
  }

  // 调整列表项的高度
  adjustItemHeights(startIndex, endIndex);
}
```

### 3. 调整列表项的高度

`adjustItemHeights` 函数用于调整动态高度的列表项。它会遍历每个新渲染的列表项，获取其实际高度，如果实际高度和预估高度不同，就会更新高度数组和占位元素的高度。

```javascript
function adjustItemHeights(start, end) {
  for (let i = start; i <= end; i++) {
    const item = content.children[i - start];
    const height = item.offsetHeight;
    if (itemHeights[i] !== height) {
      const diff = height - itemHeights[i];
      itemHeights[i] = height;
      placeholder.style.height = `${
        parseInt(placeholder.style.height) + diff
      }px`;
    }
  }
}
```

### 4. 监听滚动事件和初始渲染

最后，我们监听列表容器的滚动事件，当滚动时，就调用 `renderList` 函数进行重新渲染。同时，我们也在页面加载时，调用一次 `renderList` 进行初始渲染。

```javascript
const container = document.getElementById("list-container");
const content = document.getElementById("content");

container.addEventListener("scroll", () => {
  renderList();
});

renderList(); // 初始渲染
```

以上就是整个 JavaScript 部分的实现思路和代码分析。通过这些代码，我们实现了一个高效的虚拟列表，它可以处理大量数据，而且支持动态高度的列表项。

完整示例代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>visual-list</title>
    <style>
      #list-container {
        width: 200px;
        height: 200px;
        overflow: auto;
        border: 1px solid #ccc;
        position: relative;
      }
      #content {
        position: absolute;
        top: 0;
      }
      .list-item {
        height: 20px;
        margin: 0;
        padding: 0;
      }
      .list-item:nth-child(odd) {
        height: 60px;
        background-color: #ccc;
        margin: 0;
        padding: 0;
      }
    </style>
  </head>
  <body>
    <div id="list-container">
      <div id="content"></div>
      <div id="placeholder"></div>
    </div>
  </body>
  <script>
    const LIST_HEIGHT = 200; // 视口的高度
    const DEFAULT_ITEM_HEIGHT = 50; // 预估的列表项的高度
    const dataList = Array.from({ length: 1000 }).map((_, i) => `Item ${i}`); // 数据源
    // 初始化每个列表项的高度为预估值
    const itemHeights = Array(dataList.length).fill(DEFAULT_ITEM_HEIGHT);

    const placeholder = document.getElementById("placeholder");
    // 根据预估的高度设置占位元素的高度
    placeholder.style.height = `${itemHeights.reduce((a, b) => a + b, 0)}px`;

    function renderList() {
      const scrollTop = container.scrollTop; // 获取滚动条的位置
      let startIndex = 0;
      let endIndex = 0;
      let totalHeight = 0;

      /**
       * 计算开始和结束的索引
       * 循环首先将变量 totalHeight 初始化为零。对于 itemHeights 数组中的每个项目，循环将项目的高度添加到 totalHeight 变量中。
       * 如果 totalHeight 小于或等于列表的当前滚动位置（scrollTop），则循环将 startIndex 变量更新为当前索引。
       * 类似地，如果 totalHeight 小于或等于当前滚动位置加上列表容器的高度（scrollTop + LIST_HEIGHT），则循环将 endIndex 变量更新为当前索引。
       * 该循环的目的是确定列表中当前可见的项目。通过计算 startIndex 和 endIndex，循环可以确定哪些项目需要呈现，哪些项目可以跳过，这可以显著提高列表的性能。
       */
      for (let i = 0; i < itemHeights.length; i++) {
        totalHeight += itemHeights[i];
        if (totalHeight <= scrollTop) {
          startIndex = i;
        }
        if (totalHeight <= scrollTop + LIST_HEIGHT) {
          endIndex = i;
        }
      }

      // 根据索引渲染列表项
      content.style.transform = `translateY(${itemHeights
        .slice(0, startIndex)
        .reduce((a, b) => a + b, 0)}px)`;
      content.innerHTML = "";
      for (let i = startIndex; i <= endIndex; i++) {
        const item = document.createElement("div");
        item.className = "list-item";
        item.innerText = dataList[i];
        content.appendChild(item);
      }

      // 调整列表项的高度
      adjustItemHeights(startIndex, endIndex);
    }

    /**
     * 调整动态列表中每个项目的高度
     * 对于每个项目，函数使用 offsetHeight 属性获取项目的实际高度。如果实际高度与预估高度不同，则函数会更新高度数组和占位元素的高度。
     * itemHeights 数组存储列表中每个项目的预估高度，而 placeholder 元素是一个隐藏元素，用于维护列表容器的高度。
     * 该函数的目的是确保列表中每个项目的高度准确，以便在滚动时正确地渲染列表项。
     */
    function adjustItemHeights(start, end) {
      // 遍历每个新渲染的列表项
      for (let i = start; i <= end; i++) {
        // 获取列表项的实际高度
        const item = content.children[i - start];
        const height = item.offsetHeight;

        // 如果实际高度和预估高度不同，更新高度数组和占位元素的高度
        if (itemHeights[i] !== height) {
          const diff = height - itemHeights[i];
          itemHeights[i] = height;
          placeholder.style.height = `${
            parseInt(placeholder.style.height) + diff
          }px`;
        }
      }
    }

    const container = document.getElementById("list-container");
    const content = document.getElementById("content");

    // 监听滚动事件
    container.addEventListener("scroll", () => {
      renderList();
    });

    // 初始渲染
    renderList();
  </script>
</html>
```

## 参考文档

- [https://github.com/lkangd/infinite-scroll-sample](https://github.com/lkangd/infinite-scroll-sample "https://github.com/lkangd/infinite-scroll-sample")
- [https://github.com/dwqs/blog/issues/70](https://github.com/dwqs/blog/issues/70 "https://github.com/dwqs/blog/issues/70")
