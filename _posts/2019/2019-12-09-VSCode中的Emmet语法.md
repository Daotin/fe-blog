---
title:  "VSCode中的Emmet语法"
tags: VSCode
---

1. 目录
{:toc}

## 删除/修改节点

在 HTML 中删除 HTML 节点最麻烦的就是你需要把开、关两个节点都删除掉，否则 HTML 结构就不完整了。

**将光标定位在要删除的节点上**，通过命令 `Emmet：删除标记`，你就可以同时将开、关两个标签都删除掉。

如果要修改节点，可以使用 `Emmet：更新标签` 指令来同时修改一对开关节点（比如把div标签改为span标签）。

<!--more-->

## 包围节点

有时候需要给一对节点上插入一个父节点，我们可以选中这段 HTML 片段，在命令面板中执行 `Emmet:使用缩写包围` 命令。接着，VS Code 就会显示一个输入框，你可以在这个输入框内填入父节点的标签名称，然后会自动把我们选中的 HTML 放在其中。



## 设置Emmet语法支持

```json
"emmet.includeLanguages": {
    "javascript": "javascriptreact",
    "vue-html": "html",
    "razor": "html",
    "plaintext": "jade"
}
```

这段设置的要点就是，将某个 Emmet 默认不支持的语言，映射到一个 Emmet 支持的语言上。

比如上面的设置里，我们把 `vue-html` 映射成了 `html`，那么当你在 vue-html 使用 Emmet 时，Emmet 就会把它当作 html 来处理了。


