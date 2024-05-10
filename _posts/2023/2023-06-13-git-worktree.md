---
layout: mypost
title: 使用 Git worktree 将同一个项目分裂成多个本地目录
tags:
  - git
---

在同一个 git 仓库中，经常会建立多个分支工作。在多个分支之间切换的时候，如果一个分支不是干净的，则需要暂存再切换，有时候嫌麻烦，只能重新 git clone，这其实是有点麻烦的。

git 从 2.6.0 的版本开始增加了新的指令 `git worktree`，git worktree 的作用是将多个 working trees 附加到同一个 repository 中，允许用户一次 check out 多个分支。

git worktree 和单独 clone 项目相比，节省了硬盘空间，又因为 git worktree 使用 hard link 实现，任何一个 worktree 的提交都会无痛增加到其他的 worktree，而且速度要远远快于 clone。

## 使用

git worktree 的命令只有几行非常容易记住。

1、列出当前仓库已经存在的所有 worktree 的详细情况，包括每个 worktree 的关联目录

```
git worktree list

```

2、增加一个新的 worktree , 指定了其关联的目录是 path ，关联的分支是 <branch>

> 说明：后者是一个可选项，默认值是 HEAD 分支。如果 <branch> 已经被关联到了一个 worktree ，则这次 add 会被拒绝执行，可以通过增加 -f | --force 选项来强制执行。
> 同时，可以使用 -b <new-branch> 基于 <branch> 新建分支并使这个新分支关联到这个新的 worktree 。如果 <new-branch> 已经存在，则这次 add 会被拒绝，可以使用 -B 代替这里的 -b 来强制执行，则原来的 <new-branch> 的提交进度会被重置为和 <branch> 一样的位置。

```
git worktree add <path> [<branch>]

示例：git worktree add ../new-dir existing-branch

```

3、在删除 worktree 的关联目录之后，清除 worktree 的信息，从而使一个 worktree 完整的删除。

```
git worktree prune

```

## 总结

- git worktree 非常适合大型项目又需要维护多个分支，想要避免来回切换的情况，这里总结一些优点：
- git worktree 可以快速进行并行开发，同一个项目多个分支同时并行演进
- git worktree 的提交可以在同一个项目中共享
- git worktree 和单独 clone 项目相比，节省了硬盘空间，又因为 git worktree 使用 hard link 实现，要远远快于 clone
