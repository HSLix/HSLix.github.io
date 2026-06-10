---
title: 【一起来学 Git】LearnGitBranching-高级篇【Learn Git Together】
author: 陆爻齐-LuYaoQi
cover: /img/index/Git.png
tags:
  - Git
  - LearnTogether
categories: 
    - 一起来学 Git | Learn Git Together
series:
    - 一起来学 Git | Learn Git Together
hide: false
abbrlink: 6476fef2
date: 2025-04-30 18:27:51
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文-高级篇
> 要开始介绍 Git 的超棒特性了，快来吧！
- 该章节的内容是陆爻齐从未接触过的，不过这也正是学习的意义啊（感慨）
## 分离 HEAD
- 这里的 `HEAD` 就是 git 操作时正处于的指针位置，一般情况下，`HEAD` 会跟着分支指针走，比如在{% post_link LearnGitBranching-基础篇 %}中，每次输入命令`git checkout main`，实际上就是让 `HEAD` 和 main 同步（HEAD->main->节点），后续每次 `commit`，main 会移动，相当于两个指针会一起更新到新的节点。

- 而所谓的分离 `HEAD`，就是让 `HEAD` 离开分支头。这里就要引入一个小知识，每个 `commit` 都会有一串哈希值来对应，不过一般情况下，只要前四个字母就能确认一个 `commit`。假定某个 `commit` 的节点哈希值为 c1，那么把 `HEAD` 移到该节点的命令就是 `git checkout c1`

## 相对引用1
- 上面移动 `HEAD` 的方式需要查看 `commit` 对应的哈希值（通常用`git log`命令查看），那么有没有什么简单又强势的方法来移动 `HEAD` 呢？有的，兄弟，有的：）那就是这里的相对引用。

- 比如，我想移到 main 的上一个节点，就可以用命令`git checkout main^`，如果想移到前两个节点，多加个`^`变成`git checkout main^^`即可。如果只是移到 `HEAD` 前一个，也可以，用命令`git checkout HEAD^`就好。

## 相对引用2
- 在前一个章节，虽然学会了相对移动的 一种方式，但是如果想移动多次，比如前一百个节点（一般没这种情况罢），可以用新的符号`~`，也就是命令`git checkout HEAD~100`。

- 但是，相对引用实际上应用最多的地方是移动分支的指针，比如我想把 main 分支的指针移动到 `HEAD` 的前三个点，就用以下命令`git branch -f main HEAD~3`。注意，这里用的是 branch 而不是 checkout，私以为 checkout 是对 `HEAD` 的操作，而 branch 是对分支指针的操作。

## 撤销更改
- 两个方式 `reset` 和 `revert`，一个个介绍。

- 如果现在在本地，你想撤销 main 刚刚提交的 `commit`，可以用`git reset HEAD^`来实现，这样相当于把 main 分支指针上移，这时最新的 `commit` 的变更其实还在，只是不在暂存区了。

- 上面的 `reset` 命令本质上是通过移动分支指针的方式，但是这种方式肯定不能用于远程的仓库上，所以就需要 `revert` 命令。同样的用处，命令则是`git revert HEAD`。该命令实际上会创建新的 `commit`，该 `commit` 的内容就是撤销上一次的 `commit`。如果事项撤销上上次的 `commit`，则要写`git revert HEAD^`。这样的命令可以在远程仓库也实现撤销的操作。

