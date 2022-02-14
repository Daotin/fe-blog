
---
title: jQuery ui中sortable draggable droppable的使用
tags: jQuery
---

1. 目录
{:toc}

最近工作中用到了jQuery UI中排序和拖拽功能，花了大概一天的时间，搞清楚了大概的参数配置，以及遇到的一些问题，总结如下。

<!--more-->

## 一、sortable
简单的配置如下：

```javascript
$('#xxx-box').sortable({
    axis: 'y',
    cursor: 'ns-resize',
    placeholder: "ui-state-highlight", // 排序过程中占位符的class样式设置
    forcePlaceholderSize: true, // 强迫占位符有一个尺寸大小。
    handle:'.sort-at', // 在对象内指定的元素上开始拖动，而不是整个元素都可以拖动
    distance: 10,
    opacity: 0.8,
    containment：'parent', // 元素可以拖动排序的范围
    // helper: 'clone', // 是否clone一个元素进行拖动
    items: '.subject',  // 指定哪些元素可以排序
    stop: function (e, ui) {
        // 排序后元素的顺序（前提每个元素都需要有id属性）
        let newSubArr = $("#subs-box").sortable('toArray'); 
        console.log(newSubArr);
        // 或者
        let gs=$('#subs-box .subject');
        let newSubArr = [];

        for(let i=0;i<gs.length;i++){
            newSubArr .push({
                id: gs.eq(i).attr('id'), // 这里的id属性可以改为任意属性，比如gid等
                order:i+1
            })
        }
        console.log(newSubArr);
    },
}).disableSelection(); // 拖动时禁止选中元素
```
还有一些排序时候的事件和方法，都在参考链接的文档里面。

### 解决placeholder唯独背景色不显示的问题
> **在移动的时候强行加入一个div，设置div的颜色即可。**

```js
$("#xxx-box").sortable({
    axis: 'y',
    placeholder: "sort-bgc",
    forcePlaceholderSize: true,
    distance: 10, // 拖动距离大于15px才开始拖动
    opacity: 0.5,//拖动的透明度
    cursor: 'move', //拖动的时候鼠标样式
    'start': function (event, ui) {
        ui.placeholder.html(`<div style="height: 32px;border: 1px dashed #5b99fd;"></div>`)
    }
});
```
### 获取排序时的位置：
```js
$( "#xxx-box" ).sortable({
    start: function(event, ui) {
        ui.item.startPos = ui.item.index();  // 排序开始的位置
    },
    stop: function(event, ui) {
        console.log("Start position: " + ui.item.startPos);
        console.log("New position: " + ui.item.index()); // 排序结束的位置
    }
});
```

### **add 20201208**
有时候排序后，vue的数据变了，但是界面的元素顺序却没有变，而且使用`this.$forceUpdate()` 也无效。
经查找，发现是`v-for` 少了`key` 属性导致的，加入key属性后，问题解决。


### 遇到的问题
1、[sortable 排序的元素中有input，则点击排序元素的其他非input区域无法触发blur](https://my.oschina.net/sunchenbin/blog/632956)。

> 当鼠标点击进入input后，input得到焦点，然后我们鼠标在空白处点击一下。_（注意这里的空白处不是随便的空白处，这里的空白处是指的当前 li 元素或者其他 li 元素范围内的空白处。）_ 此时我们预测的结果是我绑定的blur事件onBlur会被触发，但实际上你会看到结果，光标仍然在input中闪烁并没有失去焦点。
>
> 解决办法：
>
> ```js
> mounted() {
>     $('.sortable-li').mousedown((e)=>{
>         // 如果点击的区域不在当前获得焦点的input中时，让当前获得焦点的input失去焦点
>         if(!document.activeElement.contains(e.target)) {
>             document.activeElement.blur();
>         }
>     });
> },
> ```
>
> 注意：`document.activeElement` : 文档中当前获得焦点的元素。




## 二、draggable

```javascript
dragInit() {
    let me = this;
    
    // 题目拖动
    $('#xxx-box  .xxx-item').draggable({
        // appendTo: ".ptype-item.radio", // 当进行拖动时，拖动组件助手应该被添加到的元素。
        // connectToSortable: "#subs-box", // 允许draggable被拖拽到指定的sortables中。
        
        // 拖动时使用的是clone的元素。如果值设置为"clone", 那么该元素将会被复制，并且被复制的元素将被拖动。
        // 之所以不使用 helper: 'clone', 是因为clone的元素没有样式，所以我们需要自定义样式，所以使用了自定义函数。
        helper: function() {
            let helper = $(this).clone();
            helper.css({'width': $(this).width(), 'background': '#fff'}); // 设置clone元素的样式
            return helper;
        },
        handle: ".drag-at", // 指定在特定的元素上触发鼠标按下事件时，才可以拖动。
        cursor: 'move',
        // containment: '.sub-box', // 可以限制draggable只能在指定的元素或区域的边界以内进行拖动。
        revert: 'invalid', // 如果设置为true，当拖动停止时，元素位置将被重置。
        revertDuration: 200,
        distance: 10,
        opacity: 0.8,
        zIndex: 10000,
        refreshPositions: true, // 所有的可拖动位置在每次鼠标移动时都会被计算。（设置该值使得drop的位置更加精确）
        start(event, ui) {
            // 开始拖拽的时候，初始化drop
            me.$nextTick(()=>{
                me.dropInit();
            });
        },
        stop(event, ui) {
            // 拖拽停止的时候，销毁drop函数。
            me.dropDestory();
        }
    }).disableSelection();

},
```


> 注意事项：
>
>  **每次dropInit函数初始化后，如果需要再次初始化，需要先销毁之前的放置对象**。否则第一次初始化后，比如某个地方A可以放置拖拽的元素，但是第二次初始化后，地方A就不可以放置了。然而实际上，如果你不把第一次初始化的dropInit函数销毁掉，地方A在第二次初始化后还是可以放置的。所以需要在拖拽停止的时候，销毁上一次的dropInit对象。


## 三、dropable
参数配置参考文档：https://www.runoob.com/jqueryui/api-droppable.html

```javascript
dropInit() {
    let me = this;

    $('#xxx-box  .xxx-item').droppable({
        // accept: '.xxx', // 控制哪个draggable元素可被 droppable 接受。
        // accept: function(d) {
        //     if($(this).hasClass('ptype'+me.selectedSubType)){
        //         console.log('d>>>>>>',$(this)[0]);
        //         return true;
        //     }
        // },
        // activeClass: 'drop-highlight', // 当一个可接受的 draggable 被拖拽时，class 将被添加到 droppable。
        // hoverClass: "drop-hover",  // 当一个可接受 draggable 被托到 droppable 上时，class 将被添加到 droppable，你可以进行高亮显示。

        // "fit"：draggable 完全重叠在 droppable 上。
        // "intersect"：draggable 重叠在 droppable 上，两个方向上至少 50%。
        // "pointer"：鼠标指针重叠在 droppable 上。
        // "touch"：draggable 重叠在 droppable 上，任何数量皆可。
        tolerance: 'pointer', // 指定使用那种模式来测试一个拖动(draggable)元素"经过"一个放置（droppable）对象

        drop: function( event, ui ) {
            // $(this) 填充到的元素
            // ui.draggable.context 填充的元素
            let dragId = $(ui.draggable.context).attr('id');
            let dropId = $(this).attr('id');

            console.log(dragId, dropId );
        },
        over(event, ui) {
            // $(this).addClass('allow-hover'); // 当拖拽元素进入可放元素时，可放置元素本身的样式，可以使用hoverClass属性代替
        },
        out() {
            // $(this).removeClass('allow-hover'); // 设置拖拽元素离开可放元素时，清除可放置元素本身的样式，可以使用hoverClass属性代替
        }
    });
},
// 移除 droppable 功能
dropDestory() {
    $('.xxx-item').droppable("destroy");
},
```



## 参考链接
- https://www.html.cn/jquery-ui-api/sortable/
- https://www.html.cn/jquery-ui-api/draggable/
- https://www.html.cn/jquery-ui-api/droppable



（完）


