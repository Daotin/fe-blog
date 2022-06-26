---
title: ElementUI中一个Select业务问题
tags: ElementUI
---


## 问题描述

今天有个需求使用到Element UI的下拉框Select，有多选功能，但是需要在点击删除的弹出提示框，在提示框中确认后才可以删除。

但是在翻遍Element UI手册后，并没有相关的事件，只有在移除某个标签后会触发`remove-tag` 事件，但是没有在移除前的相关方法。

那么，这个问题如何解决呢？



## 问题分析

**想法1**：阻止冒泡，但是好像没有地方对移除按钮使用`click.stop`。。。

**想法2**：自定义一个删除按钮，覆盖在原来的移除按钮上面。当点击自定义的按钮时弹出提示框，然后调用方法删除对应的tag数据，貌似可行，就是比较麻烦。

**想法3**：网上看到的，使用`Object.defineProperty`或者`Proxy` 拦截，在移除时，会修改数据，此时使用拦截设置操作，在里面弹框处理，感觉更麻烦了。。



## 解决方法

只要你不用 `v-model` 就没这个问题了，拆开后直接将数据绑定 `value`, 然后在 `remove-tag` 事件里是可以做判断，打开弹框，确认后再修改数据就可以了。

示例代码：

```js
// 点击删除tag icon
function removeTagClick(value) {
  readyRemoveTag.value = value; // 要移除的tag
  const defaultDeviceIds: string[] = props.deviceList ? props.deviceList.map(item => item.deviceId) : [];
  if (defaultDeviceIds.includes(readyRemoveTag.value)) {
      // 打开弹框
    addShopDialogVisible.value = true;
  } else {
    doRemoveTag();
  }
}

function doRemoveTag() {
  if (readyRemoveTag.value) {
      // 把要移除的tag过滤掉重新给value赋值即可
    formData.deviceList = formData.deviceList && formData.deviceList.filter(item => item !== readyRemoveTag.value);
  }
  if (addShopDialogVisible.value) {
    addShopDialogVisible.value = false;
  }
}
```
![image](https://user-images.githubusercontent.com/23518990/174584798-90db2d7f-5d70-43a7-b38e-1213273ae307.png)


> 参考链接：
> - https://juejin.cn/post/7067970589155131399


（完）

