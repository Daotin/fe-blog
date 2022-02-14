---
title: 一键备份github issues
tags: Github
---

<!--more-->

方法一：运行文件：backup.sh

```shell
#!/bin/sh
echo github issue 备份工具
echo 请输入您的github用户名
read username
echo 请输入您的项目名字
read repo
wget https://api.github.com/repos/$username/$repo/issues
wget https://api.github.com/repos/$username/$repo/issues/comments
```

方法二、另一个备份方案：[backup_issues](https://gitee.com/Program-in-Chinese/backup_issues)


（完）

