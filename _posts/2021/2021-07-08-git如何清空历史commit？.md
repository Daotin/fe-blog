---
title: git如何清空历史commit？
tags: Github
---

1. xxx
{:toc}

## 背景

因为之前做笔记都是在github上面，时间久了之后，commit的次数会很多，仓库的体积越来越大，这就导致每次需要clone的时候，需要等待很久才可以clone成功，花费了不少时间。

或者是当fork了别人的仓库，但是不想要别人的提交记录的时候，都需要有方法可以快速清空之前的commit记录。

本文就是教你如何使用git指令，快速清空历史commit记录。

<!--more-->

## 操作步骤

思路很简单，就是**首先创建一个独立新分支`nb`，然后把内容提交到新分支上，之后删除主分支`master`，再将当前分支 `nb` 重命名为`master`，最后强制push到远程仓库即可。**


git指令步骤如下：

1、创建一个独立新分支`nb`
```
git checkout --orphan nb
```

> 参数 `--orphan` 作用有两个，一个是拷贝当前所在分支的所有文件，另一个是没有父结点，可以理解为没有历史记录，是一个完全独立背景干净的分支。

2、把内容提交到新分支上
```
git add -A
git commit -m "first commit"
```

3、删除主分支master
```
git branch -D master
```

> `-D` 等同于 `--delete --force` 强制删除分值。

4、将当前分支nb重命名为master
```
git branch -m master
```


5、强制push到远程master仓库
```
git push -f origin master
```

完成以上5步之后，打开github仓库主页，可以看到 commit 次数为 1，内容就是我们刚刚提交的"first commit"。


![image](https://user-images.githubusercontent.com/23518990/124885974-67c01800-e006-11eb-9c4e-a0ff16a74aee.png)

到此，清空历史commit记录完成。

