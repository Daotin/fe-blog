---
layout: mypost
title: CSS实现3列网格布局的3种实现方式
tags:
  - css
---

1、Grid 布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      .parent {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 20px;
      }

      .child {
        background-color: grey;
        height: 100px;
      }
    </style>
  </head>
  <body>
    <div class="parent">
      <div class="child">1</div>
      <div class="child">2</div>
      <div class="child">3</div>
      <div class="child">4</div>
      <div class="child">5</div>
      <div class="child">6</div>
    </div>
  </body>
</html>
```

2、column 多列布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }

      /* `break-inside: avoid-column;` 是一个CSS属性，用于设置一个元素是否允许被分割到多列布局的不同列中。具体地，`avoid-column` 值表示元素不应该被分割到列的中间，而应该尽可能地保持在同一列中。

在多列布局中，如果一个元素的高度超过了一列的高度，那么浏览器会自动将它分割成几段，分别放到不同的列中。使用 `break-inside: avoid-column;` 属性可以防止这种情况发生，使得元素保持在同一列中，不被分割。

需要注意的是，这个属性只有在多列布局中才有意义，如果不是多列布局，这个属性将不起任何作用。此外，该属性可能不被所有浏览器支持，需要根据具体情况选择是否使用。 */

      .parent {
        column-count: 3;
        column-gap: 20px;
      }

      .child {
        background-color: grey;
        height: 100px;
        break-inside: avoid-column;
        width: 100%;
        margin-bottom: 20px;
      }

      .child:nth-child(n + 4) {
        margin-top: 20px;
      }
    </style>
  </head>
  <body>
    <div class="parent">
      <div class="child">1</div>
      <div class="child">2</div>
      <div class="child">3</div>
      <div class="child">4</div>
      <div class="child">5</div>
      <div class="child">6</div>
    </div>
  </body>
</html>
```

3、使用 el-row，el-col 布局
