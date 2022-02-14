---
title: 自定义浏览器滚动条样式（兼容chrome和firefox）
tags: css
---

1. 目录
{:toc}

<!--more-->

## 问题描述
浏览器默认的滚动条样式太丑了，而且不同的浏览器下滚动条的样式也不一样，为了美观和统一，必须修改滚动条的样式。

有人问，为什么不自己写一个滚动条？

其一，自己写的滚动条效率和兼容性比不上浏览器默认滚动条。

其二，自己写太麻烦了吧，能用默认的为什么不用呢 0.o

## 问题分析
既然要修改默认滚动条的样式，就需要了解滚动条的样式都有哪些属性可以修改，以及这些属性分别对应了滚动条的哪些部位？

下面有一张图可以很清楚的看到它们的对应关系：

![1254557929-5a570728e504b_articlex](https://user-images.githubusercontent.com/23518990/72045946-56ebf480-32f2-11ea-8ff2-a88ffb19f8c8.png)

属性描述：

```css
::-webkit-scrollbar    //滚动条整体部分
::-webkit-scrollbar-button   //滚动条两端的按钮
::-webkit-scrollbar-track   // 外层轨道
::-webkit-scrollbar-track-piece    //内层轨道，滚动条中间部分（除去）
::-webkit-scrollbar-thumb //滚动条里面可以拖动的那个
::-webkit-scrollbar-corner   //边角
::-webkit-resizer   ///定义右下角拖动块的样式
```

## 解决方案

于是对于chrome，我们可以这样修改滚动条样式：

```css
/*定义整个滚动条高宽及背景：高宽分别对应横竖滚动条的尺寸*/
::-webkit-scrollbar
{
    width:10px;
    background-color:#F5F5F5;
}
/*定义滚动条轨道：内阴影+圆角*/
::-webkit-scrollbar-track
{
    background-color:#F5F5F5;
}
/*定义滑块：内阴影+圆角*/
::-webkit-scrollbar-thumb
{
    border-radius:10px;
    background-color:#555;
}
```

一般我们这样设置这几个主要属性就OK了，但是如果你想更详细的css属性，也有：

```css
:horizontal //水平方向的滚动条
:vertical  //垂直 方向的滚动条
:decrement  //应用于按钮和内层轨道(track piece)。它用来指示按钮或者内层轨道是否会减小视窗的位置(比如，垂直滚动条的上面，水平滚动条的左边。)
:increment  //decrement类似，用来指示按钮或内层轨道是否会增大视窗的位置(比如，垂直滚动条的下面和水平滚动条的右边。)
:start  //伪类也应用于按钮和滑块。它用来定义对象是否放到滑块的前面。
:end //类似于start伪类，标识对象是否放到滑块的后面。
:double-button //该伪类以用于按钮和内层轨道。用于判断一个按钮是不是放在滚动条同一端的一对按钮中的一个。对于内层轨道来说，它表示内层轨道是否紧靠一对按钮。
:single-button //类似于double-button伪类。对按钮来说，它用于判断一个按钮是否自己独立的在滚动条的一段。对内层轨道来说，它表示内层轨道是否紧靠一个single-button。
:no-button //用于内层轨道，表示内层轨道是否要滚动到滚动条的终端，比如，滚动条两端没有按钮的时候。
:corner-present //用于所有滚动条轨道，指示滚动条圆角是否显示。
:window-inactive //用于所有的滚动条轨道，指示应用滚动条的某个页面容器(元素)是否当前被激活。(在webkit最近的版本中，该伪类也可以用于::selection伪元素。webkit团队有计划扩展它并推动成为一个标准的伪类)
```

用法如下：

```css
::-webkit-scrollbar-track-piece:start {}
::-webkit-scrollbar-thumb:window-inactive {}
::-webkit-scrollbar-button:horizontal:decrement:hover {}
```


## 兼容性

### IE

对于IE，目前只找到修改滚动条各种属性的颜色，未找到修改样式的地方。

具体可修改的颜色如下：

![1915790444-5a57117a9bfc5_articlex](https://user-images.githubusercontent.com/23518990/72046026-76831d00-32f2-11ea-8ada-75afe2a5c51e.gif)

有个表格看的清楚：

![1652388623-5a5711b6cf239_articlex](https://user-images.githubusercontent.com/23518990/72046028-7a16a400-32f2-11ea-931e-40e64e7d6fa8.png)


代码：

```css
.ie-div {
   scrollbar-arrow-color: #f4ae21; /*三角箭头的颜色*/   
   scrollbar-face-color: #333; /*立体滚动条的颜色*/   
   scrollbar-3dlight-color: #666; /*立体滚动条亮边的颜色*/   
   scrollbar-highlight-color: #666; /*滚动条空白部分的颜色*/   
   scrollbar-shadow-color: #999; /*立体滚动条阴影的颜色*/   
   scrollbar-darkshadow-color: #666; /*立体滚动条强阴影的颜色*/   
   scrollbar-track-color: #666; /*立体滚动条背景颜色*/   
   scrollbar-base-color:#f8f8f8; /*滚动条的基本颜色*/ 
}
```
修改后的IE滚动条样式如下：

![微信截图_20200109113915](https://user-images.githubusercontent.com/23518990/72046073-9b779000-32f2-11ea-9262-fa2a700730fd.png)


### Firefox

火狐64位目前只提供了部分自定义滚动条的属性：

- `scrollbar-width`：该属性可取值：
```
scrollbar-width: auto; // 默认值
scrollbar-width: thin; // 比默认滚动条窄
scrollbar-width: none; // 不显示滚动条，但是仍可以滚动
```

下面是 `thin` 的样式（图一默认，图二thin）：

![微信截图_20200109115611](https://user-images.githubusercontent.com/23518990/72046158-c7931100-32f2-11ea-956c-d5c980e26cd3.png)



- `scrollbar-color `： 其可填写的值有：
```
scrollbar-color: auto; // 默认值
scrollbar-color: dark;
scrollbar-color: light;
scrollbar-color: red green; // 第一个滚轮颜色，第二个滚动条背景色
```
> 其中`dark`和`light`并没有实现。

示例代码：

```
#style-0 {
    scrollbar-width: thin;
    scrollbar-color: #ccc #eee;
}
```

### 使用插件或者自己DIY

比较好用的插件：

- [JQuery Custom Scrollbar ](https://github.com/malihu/malihu-custom-scrollbar-plugin)
- [Perfect Scrollbar](https://github.com/mdbootstrap/perfect-scrollbar) ： 只有6K大小。


### 其它

我看到还有更骚的操作是在界面上套一层div，然后在滚动条的地方挖孔，只显示一部分滚动条出来，然后显示的一部分滚动条就是类似自定义的样式。。。



## 总结

如果只是修改样式的，IE算是没辙了，但是firefox还有救。

我的折中方式是，把chrome的样式设置和firefox一样。

代码：

```less
/**
描述：自定义浏览器滚动条样式。

示例：如 my-div 需要使用自定义滚动条，直接如下写法。

.my-div {
    overflow: auto;
    @import '../../commons/css/custom_scroll.less';
}

注意：元素需要使用 `overflow:hidden ` 属性。

兼容性：兼容chrome和firefox，不兼容IE

 */


&::-webkit-scrollbar {
  width: 6px;
  background-color: #eee;
}

&::-webkit-scrollbar-thumb {
  background-color: #c1c1c1;

  &:hover {
    background-color: #a8a8a8;
  }

  &:active {
    background-color: #787878;
  }
}

& {
  scrollbar-width: thin;
  scrollbar-color: #c1c1c1 #eee;
}
```
如此，chrome和firefox可算长得一样了。

![微信截图_20200109143901](https://user-images.githubusercontent.com/23518990/72046187-d2e63c80-32f2-11ea-8e55-edc778654bef.png)

---
### *(added in 20201229)*

目前的一个问题是，同一个文件下的多个块元素不能使用多次这个文件：
比如：

```css
.category-manager {
        width: 400px;
        height: calc(~'100% - 60px');
        float: left;
        border: 1px solid #ddd;
        overflow: auto;
        @import '../../resources/less/custom_scroll';
    }

    .subject-list {
        height: calc(~'100% - 60px');
        margin-left: 410px;
        border: 1px solid #ddd;
        overflow: auto;
        @import '../../resources/less/custom_scroll';
    }
```
这种情况下，第二个就会失效。

那么，如何可以使得整个页面的多个块都可以使用自定义滚动条呢？

如果在某个页面下都要用的话，那么在这个块根元素下加上这个代码（把_&_改为`*`）即可：
```css
*::-webkit-scrollbar {
  width: 6px;
  background-color: #eee;
}

*::-webkit-scrollbar-thumb {
  background-color: #c1c1c1;

  &:hover {
    background-color: #a8a8a8;
  }

  &:active {
    background-color: #787878;
  }
}

* {
  scrollbar-width: thin;
  scrollbar-color: #c1c1c1 #eee;
}
```


## 参考链接

- https://segmentfault.com/a/1190000012800450


（完）

