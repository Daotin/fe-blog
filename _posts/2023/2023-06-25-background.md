---
layout: mypost
title: background 模拟文字的下划线效果
tags: CSS
---

利用 background 系列属性，简单实现炫酷下划线效果。

源代码如下（可直接运行）：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>background 模拟文字的下划线效果</title>
    <style>
      p {
        width: 600px;
        font-size: 24px;
        color: #666;
      }
      .dv1 a {
        background: linear-gradient(90deg, #0cc, #0cc);
        background-size: 100% 2px;
        background-repeat: no-repeat;
        background-position: 100% 100%;
        color: #0cc;
      }
      .dv1 span {
        background: linear-gradient(
          90deg,
          #0cc 50%,
          transparent 50%,
          transparent 1px
        );
        background-size: 10px 2px;
        background-repeat: repeat-x;
        background-position: 100% 100%;
        color: #0cc;
      }

      .dv2 a {
        background: linear-gradient(90deg, #ff3c41, #fc0, #0ebeff);
        background-size: 0 3px;
        background-repeat: no-repeat;
        background-position: 0 100%;
        transition: 1s all;
        color: #0cc;
      }

      .dv2 a:hover {
        background-size: 100% 3px;
        color: #000;
      }

      .dv3 a {
        background: linear-gradient(90deg, #0cc, #0cc), linear-gradient(90deg, #ff3c41, #fc0, #8500d8);
        background-size: 100% 3px, 0 3px;
        background-repeat: no-repeat;
        background-position: 100% 100%, 0 100%;
        transition: 0.5s all;
        color: #0cc;
      }
      .dv3 a:hover {
        background-size: 0 3px, 100% 3px;
        color: #000;
      }
    </style>
  </head>
  <body>
    <div class="dv1">
      <h3>示例一：background 模拟文字的下划线效果</h3>
      <p>
        素胚勾勒出青花笔锋浓转淡
        <a>瓶身描绘的牡丹一如妳初妆, 冉冉檀香透过窗心事我了然</a>,
        宣纸上走笔至此搁一半
        <span>釉色渲染仕女图韵味被私藏</span>而妳嫣然的一笑如含苞待放
      </p>
    </div>

    <div class="dv2">
      <h3>
        示例二：改变 background-size 与 background-position 实现文字 hover 动效
      </h3>
      <p>
        妳的美一缕飘散 去到我去不了的地方
        <a
          >天青色等烟雨 而我在等妳, 炊烟袅袅升起 隔江千万里,
          在瓶底书汉隶仿前朝的飘逸, 就当我为遇见妳伏笔</a
        >
      </p>
    </div>

    <div class="dv3">
      <h3>示例三：下划线 hover 动效</h3>
      <p>
        天青色等烟雨 而我在等妳
        <a>月色被打捞起 晕开了结局, 如传世的青花瓷自顾自美丽 妳眼带笑意</a>
      </p>
    </div>
  </body>
</html>
```

除此之外，还可以实现文字的花式动效，具体参考下面链接：

https://mp.weixin.qq.com/s/WKzCZ31OnNa5Dm_SZj6Nnw
