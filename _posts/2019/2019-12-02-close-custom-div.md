---
title: vue中点击屏幕其他区域关闭自定义div弹出框
tags: vue
---

1. 目录
{:toc}

<!--more-->

## js获取dom方式

直接上代码：
### 方式一
```js
mounted: function () {
    let that = this;
    $(document).on('click', function (e) {
        let dom = $('.myDiv')[0]; // 自定义div的class

        if (dom) {
            // 如果点击的区域不在自定义dom范围
            if (!dom.contains((e.target))) {
                that.showMyDiv = false;
            }
        } 
    });
},
beforeDestroy() {
    $(document).off('click');
}

```

### 方式二 *(added in 20200422)*

```js
mounted() {
    window.addEventListener('click',this.close,true);
},
destroyed() {
    window.removeEventListener('click',this.close,true);
},
methods:{
    close(e) {
         // 点击的部位不是该组件根dom元素
          if (e.target !== this.$el) {
              this.showTip = false;
          } 
    }
}
```

---

## vue方式

*(added in 20200114)*

最近看代码的时候，发现了一个使用vue的方式关闭弹框，而不时使用dom的方式。

该方式更加简洁，性能也更好。

上面代码，当`showMyDiv == true` 的时候显示弹框，于是我们在整个组件页面（_如果这只是一个子组件的话，看下面补充部分_）的根元素上绑定一个点击事件：`@click=closeMyDiv` ，然后在**点击触发弹框的元素**和**弹框本身的元素**上绑定`@click.stop` ，这样我们在需要弹框的时候不会触发`closeMyDiv`事件。

过程是这样的：当点击`closeMyDiv`的时候，如果能响应，说明一定点击的是其他区域。此时判断`showMyDiv `的状态，如果为`true`，说明弹框出现了，需要消失，则设置`showMyDiv = false` 即可。

代码如下：

```js
closeMyDiv() {
     this.showMyDiv = false;
}
```
该方法不需要获取dom元素，毕竟获取dom元素会消耗浏览器性能。

### 补充 

*(added in 20200422)*

> 如果该组件只是子组件的话，就不能在整个组件页面的根元素上绑定一个点击事件，因为这个组件只是整个页面的一小部分。

处理方式：

```js
mounted() {
    window.addEventListener('click',this.close,true);
},
destroyed() {
    window.removeEventListener('click',this.close,true);
},
methods:{
    close(e) {
         this.showMyDiv = false; // 整个页面的点击都关闭
    }
}
```
然后一样，在**点击触发弹框的元素**和**弹框本身的元素**上绑定`@click.stop` 。

（完）





（完）

