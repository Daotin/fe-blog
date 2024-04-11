---
layout: mypost
title: template模板
date: 2001-01-01 12:12:12
tags:
  - 前端
---

## 几种引用方式
- 页面引用：`[[xxx]]`  
- 标题引用：`[[Topic#背景]]`
- 别名引用：`[[Topic|新的名称]]`
- 嵌入引用
	- 嵌入引用的语法`![[Note Name]]`，即在使用「双向链接」的时候，我们可以在「双向链接」前边输入一个`!`（叹号），这种「双向链接」的添加方式称为「嵌入引用」。嵌入引用带来的好处是，我们无需跳转，直接在原笔记中查看到被引入的「新笔记」。
- 块级引用
	- 块引用的语法 `[[Note Name ^]]` ，既在使用「双向链接」的时候，我们可以在「双向链接」的后边输入 `^` ，此时我们可以将被链接的笔记中的某一行（包括它的从属段落）引入到当前笔记中。这种方式成为「块引用」。

## callout

> [!warning] 这是一个callout标题
> 这里是callout里的内容
> [[#20 Callout blocks|callout]]内部支持双链
> - 同样支持==MD语法==

默认共支持**12种**类别 （可自己在 **Admonitions插件** 里添加）

参考：[Obsidian Callouts](https://notes.nicolevanderhoeven.com/Obsidian+Callouts)

1.  note
2.  abstract, summary, tldr
3.  info, todo
4.  tip, hint, important
5.  success, check, done
6.  question, help, faq
7.  warning, caution, attention
8.  failure, fail, missing
9.  danger, error
10.  bug
11.  example
12.  quote, cite


## 折叠与展开

> [!note]+ 这是一个可折叠的标题
> 这里的内容可通过点击 **Callouts标题** 折叠起来


### 多层嵌套

> [!failure] 这是第一层
> 这里是第一层的内容
>> [!bug]+ 这是嵌套的第二层
>> 这里是第二层的内容
>>> [!warning]- 这是嵌套的第三层，且默认为折叠状态
>>> 这里是第三层被折叠的内容


## 参考教程

https://publish.obsidian.md/csj-obsidian/0+-+Obsidian/Markdown/Markdown%E8%B6%85%E7%BA%A7%E6%95%99%E7%A8%8B+Obsidian%E7%89%88