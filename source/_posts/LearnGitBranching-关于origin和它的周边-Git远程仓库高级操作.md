---
title: 【一起来学 Git】LearnGitBranching-关于origin和它的周边-Git远程仓库高级操作【Learn Git Together】
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
abbrlink: 7de72648
date: 2025-05-09 10:09:43
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文
> 做一名仁慈的独裁者一定会很有趣……
- 好奇怪的引语，不过本章说是高级操作，陆爻齐在此前 git 出现奇怪问题的时候，向 gpt 请教过，给出类似的代码，有时奏效有时又寄，所以还是十分建议学习的：）
## 推送主分支
- 这章要展示的是，开发者从远程仓库获取主分支的更新，然后在本地其它分支开发完后，如何在主分支上完成更新并推送的步骤。

1. 获取主分支的更新，为了保持**线性**的提交历史，就可以采用`git pull --rebase`，注意，先切换到主分支再用 pull；

2. 然后更新本地主分支，此时主分支已经接收了远程仓库的更新。那么这里为了**线性**的提交历史，还是选择 rebase 方法，分别让其它几个分支变基（{% post_link LearnGitBranching-基础篇 %}）就行；

3. 最后推送，这个简单，`git push`;

## 合并远程仓库
- 这章和上一章要解决的是一个问题，只不过不要求**线性**的提交历史，用 merge 实现主分支的功能更新。

- merge 不说怎么搞了，但是这一章关卡过完就能够深刻地感受到，**线性**的提交历史多清晰，多好看。

## 远程跟踪分支
- 本章解释了 main，origin/main 如何关联的。其实除了之前所说的 pull 和 push 时两者的关系外，它们其实在 clone 其实会自动关联起来。

- 注意，远程分支的跟踪可以自定义，比如`git checkout -b foo origin/main`，那么就会新建一个分支 foo 跟踪远程分支 origin/main，此时要是`git pull`就会把本地没有的提交拉取下来，更新到 origin/main 和 foo 上。

- **注意**，自定义后，原来的 main 就没有跟踪 origin/main 了，所以刚刚的拉取不会更新 main。

- 同理，推送 push 时，也可以推送到远程的指定分支，比如上面关联后，`git push`就会把 foo 上新的提交推送到远程仓库的 main 上。

- 但是，上面的绑定方法也会新建分支，有没有直接绑定现有分支和远程分支的方法呢？有的：）`git branch -u origin/main foo`就能让 foo 跟踪 远程仓库的 main ，而且如果当前分支就是 foo，还能省略成`git branch -u origin/main`。

## Git push 的参数
- 有时候想不切换过去，而是直接推送一个分支上去，那就用到本章知识。

- 比如，当前的`HEAD`指针在其它地方，却想把本地的 foo 分支推到远程的 foo 分支。而如果直接写`git push`，是不会有用的，因为完整的句子其实是`git push <remote> <locale>`。这里的`<remote>`指的是目的地——远程仓库，一般写 origin 就行，`<locale>`是本地分支的名字。

## Git push 的参数2
- 上面的 push 参数能够让指定分支推送上去，但有时候在其他分支开发的东西，想推送到远程仓库的指定分支，这个时候就可以设定 push 的参数来实现。

- 比如`git push origin foo^:main`就会把 foo 的上一个提交之前的远程仓库没有的提交推送到远程仓库的 main 分支上，如果远程仓库没有 main 分支，就会新建一个 main 分支获取推送信息。

## Git fetch 的参数
- 与上面的 push 类似，拿例子比较好理解。

- 对于`git fetch origin foo` 则会把远程仓库中`foo`的本地没有的提交给 fetch 下来，更新到 origin/foo 上。

- 对于`git fetch orgin main^:foo`则会把远程仓库的`main`分支上一个提交之前的本地没有的分支 fetch 到本地的 foo 分支上，注意，是 foo 和 origin/main 上，不是 origin/foo。（这个操作几乎用不到，但语法是支持的）

- 最后，绝大部分情况下，一个`git fetch`拉取那些更新就行，不用搞上面那些虚头八脑的。

## 没有 source 的 source
- 这里看网站内容才能理解标题的含义，下面主要简单说下知识点。

- Git 支持下面删除远程分支的方式`git push origin :foo`就会把远程仓库中的 foo 分支给删除。

- 同时也支持下面创建新分支的方式`git fetch origin :bar`就会在本地新建一个叫 bar 的分支

## Git pull 的参数
- 前面（{% post_link LearnGitBranching-Push-Pull-Git远程仓库 %}）说过，pull 就是 fetch +（merge || rebase），所以这里加的参与与 fetch 类似。

- 比如`git pull origin foo`就会等于`git fetch origin foo` + `git merge origin/foo`，`git pull origin main^:bar`就是`git fetch origin main^:bar`+`git merge bar`。