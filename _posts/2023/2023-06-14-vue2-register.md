---
layout: mypost
title: vue2 自动注册全局组件
tags:
  - vue
---

```js
/**
 * 全局注册 components 文件夹下的组件
 */
import Vue from 'vue';

const compRegist = {
  install() {
    const requireComponent = require.context(
      // 其组件目录的相对路径
      '../components',
      //是否查询子目录
      true,
      // 匹配基础组件文件名的正则表达式
      /.vue$/
    );

    requireComponent.keys().forEach((fileName) => {
      // 获取组件配置
      const componentConfig = requireComponent(fileName);

      // 获取组件的 PascalCase 命名
      const componentName = window.$_.upperFirst(window.$_.camelCase(fileName.split('/').pop().split('.')[0]));
      // console.log("⭐componentName==>", componentName);

      // 全局注册组件
      Vue.component(
        componentName,
        // 如果这个组件选项是通过 `export default` 导出的，
        // 那么就会优先使用 `.default`，
        // 否则回退到使用模块的根。
        componentConfig.default || componentConfig
      );
    });

    console.log('%c==⭐Global component registration completed!⭐==', 'background:#0f0;');
  },
};

export default compRegist;

/*
在main.js中引用:
import _ from "lodash";
window.$_ = window._ = _;
import compRegist from "./utils/compRegist";
Vue.use(compRegist);
*/
```
