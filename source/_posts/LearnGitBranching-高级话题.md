---
title: 【一起来学 Git】LearnGitBranching-高级话题【Learn Git Together】
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
abbrlink: 5aa3ef8b
date: 2025-05-06 14:15:30
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文-高级话题
> 只为真正的勇士
- 说是高级话题，实际上就是一些 Git 版小测，把之前的命令做了一点点的变化

## 多次 Rebase
- 题目说的很明白，“把所有这些分支上所做的工作都通过 rebase 合并到 main 分支上”，无需多盐。

- 对于 rebase 为什么有这样的效果，可以看看此前基础篇相关解释（{% post_link LearnGitBranching-基础篇 %}）

## 两个 Parent 节点
- 此前没怎么提到 parent 节点这个概念，实际上，一旦在当前的 HEAD 上 commit 一下，现在的节点就是新节点的 parent 节点。

- 而出现两个 parent 节点的情况，就是 merge 命令执行后的交点部分。

- 现在回想下之前学过的相对引用（{% post_link LearnGitBranching-高级篇 %}，比如命令`git checkout HEAD^`这样的命令会在两个 parent 节点中如何选择呢？

- 答案是，他会自动选择比较早的节点，不过通过命令`git checkout HEAD^2`可以选择到第二早的节点，`^`后面的参数不是对几个节点操作的含义（要与`~`有所区分）而是选择第几个 parent 节点的意思。

- 此外，相对引用支持链式操作，比如本题答案就可以用下面这一条命令来结束`git branch -f bugWork HEAD~^2~`

## 纠缠不清的分支
- 这一题就是 cherry-pick 和分支移动的简单应用，只可惜 cherry-pick 这个命令只能对 HEAD 指着的地方做操作，不然还会更舒服：）

