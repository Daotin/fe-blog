---
layout: mypost
title: win10日历聚光灯效果
tags: CSS
---

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
      .grid-body {
        width: 300px;
        height: 300px;
        padding: 10px;
        box-sizing: border-box;
        position: relative;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 10px;

        cursor: default;
        color: rgba(255, 255, 255, 0.8);
        background-color: rgb(60, 60, 60);
      }

      .grid-item {
        border: 3px solid transparent;
      }

      .grid-item:hover {
        border: 3px solid rgba(255, 255, 255, 0.3);
      }

      .grid-mask {
        width: 100%;
        height: 100%;
        padding: 10px;
        box-sizing: border-box;
        position: absolute;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 10px;
        /* 上面的代码是为了保持两层一致 */

        /* 设置mask-image */
        /* 光圈颜色 */
        background: transparent;
        -webkit-mask-image: radial-gradient(
          circle at center,
          white 0%,
          white 80px
        );
        -webkit-mask-image: radial-gradient(
          circle at center,
          white 0%,
          transparent 80px
        );
        -webkit-mask-repeat: no-repeat;
        -webkit-mask-size: 160px 160px;
        pointer-events: none;
      }

      .grid-mask div {
        border: 3px solid rgba(255, 255, 255, 0.5);
      }
    </style>
  </head>
  <body>
    <!-- 为了保持一致，大家都要九个格子哦！ -->
    <div class="grid-body">
      <div class="grid-mask">
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
      </div>
      <div class="grid-item">1</div>
      <div class="grid-item">2</div>
      <div class="grid-item">3</div>
      <div class="grid-item">4</div>
      <div class="grid-item">5</div>
      <div class="grid-item">6</div>
      <div class="grid-item">7</div>
      <div class="grid-item">8</div>
      <div class="grid-item">9</div>
    </div>
  </body>
  <script>
    let grid = document.querySelector(".grid-body");
    let maskLayer = document.querySelector(".grid-mask");
    let bounding = maskLayer.getBoundingClientRect();
    console.log("⭐==>", bounding);
    grid.addEventListener("mousemove", (e) => {
      let x = e.pageX;
      let y = e.pageY;
      maskLayer.style.webkitMaskPosition = `${x - bounding.x - 80}px ${
        y - bounding.y - 80
      }px`;
    });
  </script>
</html>
```

示例效果：

![](/image/2023/2023-08-15-17-00-49.png)
