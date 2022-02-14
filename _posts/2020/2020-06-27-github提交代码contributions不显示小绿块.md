---
title: github提交代码contributions不显示小绿块
tags: github git
---

1. 目录
{:toc}

## 问题描述：

最近发现一个问题就是不管是提交新增的代码还是修改后提交的代码在github的contributions上都不显示贡献小绿块。

于是我在 github help 里面找到了答案：

官方链接如下：[https://help.github.com/articles/changing-author-info/](https://help.github.com/articles/changing-author-info/)

<!--more-->

## 问题分析：

主要原因是：提交代码的邮箱与创建时的邮箱地址不一样。

## 解决办法：

1、从github仓库下载一份代码，如果本地已经存在，使用git pull 保证和git仓库的代码同步。

2、将下面的代码保存为一个脚本，修改其中的

- OLD_EMAIL 为你提交代码时错误的邮箱地址
- CURRENT_NAME 为正确的用户名
- CURRENT_EMAIL 为正确的邮箱地址

```bash
#!/bin/sh
git filter-branch --env-filter '
OLD_EMAIL="错误的邮箱地址"
CORRECT_NAME="正确的用户名"
CORRECT_EMAIL="正确的邮件地址"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

然后在git 终端里面执行这段脚本。

3、输入下面代码将正确的信息 push

```
git push --force --tags origin 'refs/heads/*'
```

4、去自己的github仓库即可看到小绿块出现了。




（完）

