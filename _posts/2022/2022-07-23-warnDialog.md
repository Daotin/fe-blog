---
layout: mypost
title: 函数式弹框组件warnDialog实现
tags: Vue
--- 


## 背景

有一个需求，在项目中，不管在任何页面，如果超时，那么在下次调用接口的时候，都需要弹出提示框，告知已经超时，然后弹框中有个按钮，可以跳转到登录页。

当然，可能你有疑问，为什么超时不直接跳转到登录页，还要有个提示呢？这是有其他业务原因，这里就省略的，反正就是要实现这个需求。



## 问题分析

因为项目中是弹框是封装的`el-dialog`，但是el-dialog不像ElMessage，可以使用全局方法那样去调用，例如：

```javascript
import { ElMessage } from 'element-plus'
ElMessage.success(options)
```

即使我们封装了`el-dialog`，它也只是个组件，如何在任何页面都可以显示呢？

所以现在的问题是，如何用js的方式创建一个组件？



## 实现

在Vue3中，可以使用[createApp](https://v3.cn.vuejs.org/api/global-api.html#createapp "createApp")来创建一个组件实例。

```javascript
import { createApp } from 'vue'

const app = createApp({})
```

该函数接收一个`根组件选项对象`作为第一个参数，使用第二个参数，我们可以将根 prop 传递给应用程序。

那么就有思路了：

1.  创建弹框组件实例

2.  创建渲染节点

3.  将实例挂载到页面节点上



### 创建弹框组件实例

Modal就是我们封装el-dialog的组件。

```javascript
import { createApp, provide } from 'vue';
import Modal from './Modal.vue';

function openModal () {
    // 1. 创建弹框组件实例
    const modalApp = createApp(Modal);
    // 2. 创建渲染节点
    // 3. 将实例挂载到页面节点上
}
```

传递参数给弹框组件的props属性。

```javascript
function openModal (options = {}) {
    const modalApp = createApp(Modal, {
        // 控制弹框是否显示
        modelValue: true,
        // 传入弹框标题
        title: options.title || 'title',
        // 传入关闭弹框的方法，关闭弹框就是将弹框实例卸载下来
        close: () => {
            // 将弹框实例卸载
            modalApp.unmount(dom);
            // 删除页面节点
            document.body.removeChild(dom);
        }
    })
}
```



### 创建渲染节点

```javascript
function openModal () {
    // 1. 创建弹框组件实例
    ...
    // 2. 创建渲染节点
    const dom = document.createElement('div');
    document.body.appendChild(dom);
    
    // 3. 将实例挂载到页面节点上
}
```



### 将实例挂载到页面节点上

```javascript
function openModal () {
    ...
    
    // 3. 将实例挂载到页面节点上
    modalApp.mount(dom);
}
```



这样我们就完成了弹框实例的挂载渲染，并且可以通过`openDialog`方法进行函数式调用。

```javascript
<template>
    <button @click="open">打开弹框</button>
</template>
<script setup>
    import openDialog from '../components/Modal';
    
    const open = () => {
        openDialog({
            title: '标题',
            content: '内容'
        });
    }
</script>
```

在Modal组件中点击关闭，调用`prop.close`方法即可：

```javascript
function handleClose(done) {
  props.close();
}
```



## 发现问题

如何判断页面是否超时，一般是在调用接口的时候，后端会返回特定的code，表示登录超时，我们可以响应拦截器中进行处理。

```javascript
  // 响应拦截器
  private setResInterceptors = () => {
    this.instance.interceptors.response.use(
      (res) => {
        const { code = 200, body = res, message } = res.data
        switch (code) {
          case 200:
            return Promise.resolve(body)
          case 401:
            window.$message.warning(message || '无权限')
            const appStore = useAppStoreWithOut()
            // 集中登录的处理
            if(appStore.isSsoLogin) {
              appStore.timeoutQuit({
                title: '温馨提示',
                desc: '子系统登录已超时。确认并关闭子系统？',
                img: timeoutTip,
              })
            } else {
              appStore.logout(false)
            }
            return Promise.reject(res)
          default:
            window.$message.error(message || '响应失败')
            return Promise.reject(res)
        }
      },
      (err) => {
        if (!axios.isCancel(err)) {
          window.$message.error('响应失败')
        }
        return Promise.reject(err)
      }
    )
  }
```

其中，我们调用了一个自定义的方法`timeoutQuit`，`timeoutQuit`的实现如下：

```javascript
import openDialog from '@/components/base/warnDialog/index';

// 集中平台登录的情况，子系统登录超时退出处理
timeoutQuit(config) {
  openDialog({
    ...config,
    comfirmCallBack() {
      localMng.removeItem(SsoLoginName);
      if(window.opener) {
        window.close()
      } else {
        router.replace('/error/transfer');
      }
    }
  })
}
```

在`timeoutQuit`里面，调用了`openDialog`，传入了参数中的config，这样使用时没有问题的。



问题在于，但是在页面超时后刷新的时候，有大量的请求都会是code为401的，导致会创建大量的弹框。

那么，如何只产生一个弹框？



### 单例模式

所以我们的函数openModal 就要换成类的写法，来实现`单例模式`。代码如下：

```javascript
import { createApp, provide } from 'vue';
import WarnDialog from './warnDialog.vue';

class CustomModal {
  // 静态属性
  static instance: CustomModal;

  constructor(config) {
    this.openModal(config)
  }

  openModal(config) {
    // 1. 创建弹框组件实例
    let  dom = document.createElement('div');
    const modalApp = createApp(WarnDialog, {
      ...config,
      close: () => {
        // 将弹框实例卸载
        modalApp.unmount();
        // 删除页面节点
        document.body.removeChild(dom);
      }
    });
    // 2. 创建渲染节点
    document.body.appendChild(dom);
    // 3. 将实例挂载到页面节点上
    modalApp.mount(dom);
  }

  //静态方法
  static getInstance(config) {
    if(!CustomModal.instance) {
      CustomModal.instance = new CustomModal(config);
    }
    return CustomModal.instance;
  }
}

// 最后导出类的静态方法来创建一个单一实例。
export default CustomModal.getInstance;
```

最后导出类的静态方法来创建一个单一实例。



## 继续优化

到目前为止，我们使用组件的时候还是需要引入方法，为了方便全局使用，我们可以进一步优化，将openDialog方法注册到全局属性中去。

那么我们在什么时候完成注册的操作呢？当我们使用第三方插件的时候，经常会使用到`app.use`方法。

其实use方法就是Vue提供给我们来注册插件的，use方法会先判断插件有没有被注册，如果没有注册，会调用插件的install方法，**如果插件不是对象，本身就是个方法，那么就执行这个方法**。

所以我们可以添加一个`dialogInstall`方法，在use执行的时候，将openDialog方法注册到全局。

```javascript
import { createApp, provide } from 'vue';
import WarnDialog from './warnDialog.vue';

class CustomModal {
  //...
}

function dialogInstall (app) {
    console.log('dialogInstall was invoked');
    app.provide('OPENDIALOG', openModal)
}

export {
  CustomModal.getInstance,
  dialogInstall
};
```

在main.js中引入dialogInstall方法并注册。

```javascript
import {dialogInstall} from './components/Modal';
...
app.use(dialogInstall).use(router).mount('#app');
```

当我们刷新页面的时候，可以看到“dialogInstall was invoked”这句话在控制台中打印出来了，说明dialogInstall方法在注册的时候确实被执行了。

那么在其他组件中，我们使用inject获取到openModal方法进行调用。

```javascript
const openDialog = inject('OPENDIALOG');

// 集中平台登录的情况，子系统登录超时退出处理
timeoutQuit(config) {
  openDialog({
    ...config,
    comfirmCallBack() {
      localMng.removeItem(SsoLoginName);
      if(window.opener) {
        window.close()
      } else {
        router.replace('/error/transfer');
      }
    }
  })
}
```

我们这里是利用provide和inject来实现全局属性的传递，大家也可以使用`app.config.globalProperties`来挂载openDialog方法，这里就省略了。

（完）

