---
layout: mypost
title: 通过npm i 安装某些包后（比如webpack），为什么可以在scrips中直接run使用？
tags:
  - npm
---

在项目的package.json中：

```json
"scripts": {
 "build": "webpack"
}
```

### 问题1：npm run build执行原理是什么？

上面代码，当我们使用npm run build的时候，其实npm 会自动查找并执行位于 ./node_modules/.bin/ 目录下的对应webpack命令，

实际运行的就是`./node_modules/.bin/webpack`指令。

原理：当你在项目中使用 npm run 运行脚本时，npm 会修改 PATH 环境变量，临时将 ./node_modules/.bin/ 目录添加到 PATH 的前面。这意味着命令行会首先在这个目录下搜索可执行文件。

### 问题2：当使用npm i webpack的时候，为什么会在./node_modules/.bin/中存在webpack指令？

当你使用 npm install webpack 安装 webpack 或其他任何 npm 包时，如果该包包含一些可执行文件（命令行工具或脚本），npm 会自动将这些可执行文件的链接（符号链接，即快捷方式）创建在 `./node_modules/.bin/` 目录中。

比如在webpack的 npm 包的 package.json 文件中，可以有一个 bin 字段。这个字段指定了包提供的命令行工具的名字和相应的文件路径。例如，webpack 的 package.json 中可能类似这样定义：

```json
"bin": {
  "webpack": "bin/webpack.js"
}

```

当你安装包时，npm 会读取 bin 字段，并在 ./node_modules/.bin/ 目录中为指定的文件创建符号链接。这些链接的名字将是 bin 字段中定义的命令名（如 webpack），而链接的目标则是实际的执行文件路径。

所以最终当你，npm run build的时候，实际执行的是`./node_modules/.bin/webpack`指令快捷方式，实际执行的是`./node_modules/webpack/bin/webpack.js`文件。




