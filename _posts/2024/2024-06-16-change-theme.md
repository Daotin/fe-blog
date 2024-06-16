---
layout: mypost
title: 前端项目切换主题色方案
tags:
  - 前端
---


前端项目如何切换主题色？

## 1、直接使用element ui官方推荐的主题配置

通过使用在线主题编辑器，然后下载主题包，最后引入自定义主题。

引入自定义主题和引入默认主题一样，在代码里直接引用「在线主题编辑器」生成的主题的 `theme/index.css` 文件即可。

```js
import Vue from 'vue'
import ElementUI from 'element-ui'
import '../theme/index.css'

Vue.use(ElementUI)
```

## 2、比较low的方式，使用css变量一个个替换。

之前有个做的项目，要求增加亮色和暗色主题，我就采用的是使用`css变量`全局替换使用的方式。

```less
// 全局颜色
:root {
  // 默认深色主题
  --theme-blue: #1890ff;
  --theme-blue-hover: #40a9ff;
  --theme-blue-active: #40a9ff;
  --theme-blue-disabled: #484b65;
  
  //...
}

// 浅色主题
.light {
  --theme-blue: #1890ff;
  --theme-blue-hover: #3da2ff;
  --theme-blue-active: #1482e6;
  --theme-blue-disabled: #8ac6ff;

  //...
}
```

然后项目中所有用到颜色的地方，都使用css变量的形式：

```less
// 自定义code样式
.json-theme {
  background: var(--second-bgc);
  white-space: nowrap;
  color: var(--text-2);
  font-size: 14px;
  font-family: Consolas, Menlo, Courier, monospace;
}
</style>
```

当然，这还好，问题最大的是，所有的element ui 的组件要使用css变量覆盖，那工作量就很大了！！：

```less
// 比如下拉框
/*******下拉框********/
.el-select-dropdown {
  background-color: var(--second-bgc-hover);
  border-color: var(--second-bgc-hover);
  .el-select-dropdown__wrap {
    ul {
      li {
        background-color: var(--second-bgc-hover);
        color: var(--text-1);
        &:hover {
          background-color: var(--second-card-hover);
        }
        &.selected {
          background-color: var(--second-card-hover) !important;
        }
        &.is-disabled {
          color: var(--text-3);
        }
      }
    }
  }
  .popper__arrow {
    border-bottom-color: var(--second-bgc-hover) !important;
    &::after {
      border-bottom-color: var(--second-bgc-hover) !important;
    }
  }
}
```

然后这个文件就写了1500+行，而且是不可能穷尽所有的变量进行覆盖的，很多没有用到的class就没有覆盖，所以可能会有遗漏的问题！

## 2.1 还是使用css变量，只不过是替换element ui原有的变量，这样的话，就不需要修改elementui组件的样式了

所有的elementui自定义变量地址：`/node_modules/element-ui/packages/theme-chalk/src/common/var.scss`

直接新起一个文件进行替换覆盖即可。
