---
title: 你可能不知道的css-doodle
tags: css
---

1. 目录
{:toc}


好久没写文章了，下笔突然陌生了许多。

第一个原因是刚找到一份前端的工作，业务上都需要尽快的了解，第二个原因就是懒还有拖延的习惯，一旦今天没有写文章，就由可能找个理由托到下一周，进而到了下一周又有千万条理由拖到下下一周，所以解决的办法就是当成任务来做，让自律成为一种习惯，做起事来就不会有太大的抱怨。

行动起来，以后每周至少出一篇文章，输出倒逼输入，这也是更好学习的一种方式。

<!--more-->

今天的主角是css-doodle，不知道有多少人知道的，反正我是第一次看到这个东西。

起因很简单，大家都知道现在建立自己个人博客一个很方便免费的途径就是使用Github Page来搭建自己的个人博客，但是配置博客的过程却让人特别烦恼，需要根据一个json文件配置博客的标题头像分类等等，然后每个主题也需要配置各种属性，而且在网上找到的教程里面每个人都是根据自己的喜好编写的一套配置，基本上不存在通用性。

于是我在想，有没有一种图形化的工具来进行这些配置呢？

还真让我找到了，这个工具就是 gridea ，官方网站是 https://gridea.dev/

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190355442-1480041897.png)



这个客户端可以很方便的帮我们配置一些必要的网站配置，比如头像目录分类等等，而图形化的方式让我们专注于写作而不是网站的一些配置，个人觉得非常方便，有兴趣的可以试试。

但是今天的主角不是她（虽然很优秀），而是她后面的背景图片，我当时不知道为什么觉得背景很好看，就想把她扣下来，作为前端都知道，点击鼠标右键，如果插入的是img标签的话，可以直接保存图片，如果没有的话，那可能是插入的背景图片，可以右键打开检查，找到当前的元素对应的样式，如果是插入的背景图，在背景图的链接上右键在新标签页打开，然后右键保存图片即可。

然而，当我检查元素的时候，发现并没有我想要的背景图，咦，那这到底是啥东东呢？

于是我发现了这个css-doodle元素，把这个标签删除后，果然背景就没了。

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190405937-1560637766.png)



果然是这个东西在捣鬼。

于是就有了本文，我们来稍微看看这是个什么东东。

官网如下：https://css-doodle.com/

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190414890-322851268.png)



官方介绍是：A web component for drawing patterns with CSS. 一个绘制css图案的组件。

先来看看怎么使用：

首先使用script引入这个库文件：

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.6.1/css-doodle.min.js"></script>
// or
<script src="https://unpkg.com/css-doodle@0.6.1/css-doodle.min.js"></script>
```

然后定义一个`<css-doodle></css-doodle>`标签，用于容纳所绘制的图案。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <css-doodle></css-doodle>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.6.1/css-doodle.min.js"></script>
</html>
```

容器有了，之后就是最重要的绘制图案了。

我们先找一个简单的示例分析，然后做一个自己的图案出来。

```html
<css-doodle>
    :doodle {
        @grid: 5;
        @size: 30vmax;
        grid-gap: 1px;
        background: #f5f5f5;
    }
    background-color: hsla(@r(0,360), 70%, 70%, @r(.1,1));
    transform: scale(@rand(.2, 1));
</css-doodle>
```

然后我们可以得到下面的图案：

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190426470-587435871.png)



结合官网分析代码：

```css
:doodle {
    @grid: 5;      
    @size: 30vmax;
    grid-gap: 1px;
    background: #f5f5f5;
}
```

> `:doodle` : 表示的是css-doodle元素
>
> `@grid` : 图案行列均为5，即为5x5的图案
>
> `@size` : 每一个图案的大小。vmax表示相对于视口的宽度或高度中较大的那个。例如如果当前视口宽度500px，高度200px，那么以视口宽度为参考，于是1vmin=5px。
>
> `grid-gap` : 每个图案的间隔为1px

```css
background-color: hsla(@r(0,360), 70%, 70%, @r(.1,1));
```

> `@r` : 即`@rand`的缩写，用法`@rand(start,end)`，表示从start到end的随机值。

于是我们就可以得到上面的图案。



除了上面一些简单的属性之外，css-doodle官网还介绍了很多属性：

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190438134-657721060.png)



下面说几个常用到的：

### @nth,@even,@odd,@at

> `@nth` `@even`  `@odd`  `@at` 都是对整个坐标下的图案进行选择的。
>
> 例如：
>
> ```css
> /*对第五个图案进行选择*/
> @nth(5) {
>   background: #60569e;  
> }
> 
> /*选择第四行，第二列的图案*/
> @at(4, 2) {
>   background: #60569e;
> }
> ```
>
> 



### @grid

设置行列个数

比如：

```css
:doodle {
    @grid: 3x3; /*三行三列*/
    @size: 8em;
}
```

如果行列相同，可以省略一列，而且还可以和每一个图案的大小写在一起：

```css
:doodle {
    @grid: 8 / 8vmax;  /*8行8列，每个图案大小为8vmax*/
}
```



### @use

除了把样式写在css-doodle标签内之外，还可以将样式提出像style的方式书写，如上面的例子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        :root {
            --rule-a: (
                :doodle {
                    @grid: 5;
                    @size: 30vmax;
                    grid-gap: 1px;
                    background: #f5f5f5;
                }
                background-color: hsla(@r(0,360), 70%, 70%, @r(.1,1));
                transform: scale(@rand(.2, 1));
            )
        }
    </style>
</head>
<body>
    <css-doodle>
        @use: var(--rule-a);
    </css-doodle>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.6.1/css-doodle.min.js"></script>
</html>
```



### @size,@min-size,@max-size

设置一个图案的大小，最小值和最大值。



###  @place-cell

设置当前图案相对于当前单元格的相对位置：

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190452840-1215313071.png)




Function的属性就很多了，具体可以到官网查看，有很详细的使用说明。

![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190501701-1878836020.png)





怎么让图案动起来？

css-doodle还提供了JS API，使用js来控制图案的显示。

```js
const doodle = document.querySelector('css-doodle');  // 选择当前的css-doodle元素

doodle.grid = "5";   // 设置行列个数

console.log(doodle.grid);  // 在控制台打印当前的行列个数

doodle.use = 'var(--my-rule)';  // 指定当前css-doodle要显示的图案css

doodle.update(                // 更新样式
    `                 
  :doodle { @grid: 6 / 8em }
  background: rebeccapurple;
  margin: .5px;
`);

doodle.update();        // 刷新样式
```



有了这个知识，我们模仿 Gridea 主页做一个背景图，下面是我用emoji表情做的背景图：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        :root {
            --rule-emoji :(
                :doodle {
                    @grid: 20 / 100vmax;
                    grid-gap: 1px;
                    background: #f9d654;
                    overflow: hidden;
                }
                transition: @r(1s) ease;
                transform: scale(@rand(.1, 1)) rotate(@rand(0, 360)deg);
                :after {
                    content: \@hex(@rand(0x1F600, 0x1F636));  /*将十六进制转换成unicode编码*/
                    opacity: @r(.1,1);
                    font-size: 3vmax;
                }
            )
        }
    </style>
</head>
<body>
    <css-doodle>
        @use: var(--rule-emoji);
    </css-doodle>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.6.1/css-doodle.min.js"></script>
<script>
    const doodle = document.querySelector('css-doodle');
    // 每次点击刷新css-doodle
    doodle.onclick = function () {
        doodle.update();
    };
</script>
</html>
```
![](https://img2018.cnblogs.com/blog/754332/201904/754332-20190421190512976-1552616341.png)



（完）

