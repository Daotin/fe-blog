---
layout: mypost
title: vue2中通过注册插件，达到函数式调用loading等组件的方式
tags: [vue]
---

1、首先，我们实现一个loading的vue组件（仿element的风格），代码如下：
```html
<template>
	<transition name="fade">
		<div
			v-show="visible"
			class="full-screen-loading"
			:class="{ 'dark-bg': background === 'dark', 'light-bg': background === 'light' }">
			<div class="loading-wrapper">
				<svg class="circular" viewBox="0 0 50 50">
					<circle class="path" cx="25" cy="25" r="20" fill="none"></circle>
				</svg>
				<p v-if="text" class="loading-text">{{ text }}</p>
			</div>
		</div>
	</transition>
</template>

<script>
export default {
	name: 'FullScreenLoading',
	props: {
		visible: {
			type: Boolean,
			default: false,
		},
		background: {
			type: String,
			default: 'dark',
			validator: function (value) {
				return ['dark', 'light'].indexOf(value) !== -1
			},
		},
		text: {
			type: String,
			default: '加载中...',
		},
	},
}
</script>

<style scoped>
.full-screen-loading {
	position: fixed;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	z-index: 9999;
	display: flex;
	align-items: center;
	justify-content: center;
	transition: background-color 0.3s;
}

.dark-bg {
	background-color: rgba(0, 0, 0, 0.7);
}

.light-bg {
	background-color: rgba(255, 255, 255, 0.7);
}

.loading-wrapper {
	display: flex;
	flex-direction: column;
	align-items: center;
}

/* SVG 样式 */
.circular {
	width: 42px;
	height: 42px;
	animation: loading-rotate 2s linear infinite;
}

.path {
	stroke-width: 4;
	/* 设置渐变色效果 */
	stroke: #409eff;
	/* 设置圆角端点 */
	stroke-linecap: round;
	/* 添加 dash 效果 */
	stroke-dasharray: 90, 150;
	stroke-dashoffset: 0;
	animation: loading-dash 1.5s ease-in-out infinite;
}

.loading-text {
	margin-top: 10px;
	font-size: 14px;
	color: #409eff;
}

/* 转圈动画 */
@keyframes loading-rotate {
	100% {
		transform: rotate(360deg);
	}
}

/* 圆环动画 */
@keyframes loading-dash {
	0% {
		stroke-dasharray: 1, 200;
		stroke-dashoffset: 0;
	}
	50% {
		stroke-dasharray: 90, 150;
		stroke-dashoffset: -40px;
	}
	100% {
		stroke-dasharray: 90, 150;
		stroke-dashoffset: -120px;
	}
}

/* 过渡动画 */
.fade-enter-active,
.fade-leave-active {
	transition: opacity 0.3s;
}

.fade-enter,
.fade-leave-to {
	opacity: 0;
}
</style>

```

2、然后实现一个 Vue 插件，通过 this.$loading 来调用 loading 组件。
```js
// loading/index.js
import LoadingComponent from './Loading.vue'  // 这里引入上一个回答中的 Loading.vue 组件

let loadingInstance = null

const Loading = {
  install(Vue) {
    // 1. 创建一个Vue构造器
    const LoadingConstructor = Vue.extend(LoadingComponent)

    // 2. 定义一个创建loading的函数
    function initInstance() {
      // 创建loading实例
      loadingInstance = new LoadingConstructor({
        el: document.createElement('div'),
        data() {
          return {
            visible: false,
            background: 'dark',
            text: '加载中...'
          }
        }
      })
      
      // 将元素添加到body中
      document.body.appendChild(loadingInstance.$el)
    }

    // 3. 定义显示和隐藏的方法
    function show(options = {}) {
      if (!loadingInstance) {
        initInstance()
      }
      // 合并默认配置和传入的配置
      if (typeof options === 'string') {
        loadingInstance.text = options
      } else if (typeof options === 'object') {
        const { background = 'dark', text = '加载中...' } = options
        loadingInstance.background = background
        loadingInstance.text = text
      }
      loadingInstance.visible = true
    }

    function hide() {
      if (loadingInstance) {
        loadingInstance.visible = false
      }
    }

    // 4. 添加实例方法
    Vue.prototype.$loading = {
      show,
      hide
    }
  }
}

export default Loading
```

3、然后在 main.js 中注册插件：
```js
// main.js
import Vue from 'vue'
import Loading from './components/loading'

Vue.use(Loading)
```

4、然后就可以在vue组件中使用了

```html
// 基础用法
this.$loading.show()  // 显示loading
this.$loading.hide()  // 隐藏loading

// 传入配置
this.$loading.show({
  text: '正在提交...',
  background: 'light'
})

// 只传文字
this.$loading.show('正在加载...')
```

示例图：
![image](https://github.com/user-attachments/assets/7512e12a-b5a4-4852-9e53-e1fe02ebb911)


