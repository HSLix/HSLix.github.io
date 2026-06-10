---
title: 【一起来学 Git】LearnGitBranching-完结撒花与感言【Learn Git Together】
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
abbrlink: 73d3bb24
date: 2025-05-09 14:01:07
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文
- 经过大概一周有余的更新，这个系列就算是完结了（8787878787），对陆爻齐自身而言算是受益匪浅啊。

- 其实吧，陆爻齐原本最熟悉的部分大多在{% post_link LearnGitBranching-基础篇 %}和{% post_link LearnGitBranching-Push-Pull-Git远程仓库 %}，前者很多命令本地用，后者主要使用 pull 和 push。 


- 但是吧，有时候遇到一些想编辑 commit 顺序的时候就很难受，问 ai 吧，操作完又不知所以然，有时候 ai 也不会。现在总算是对 git 有了更多的了解，至少这一点点的基础，看着网上的帖子和官方文档也不会太痛苦了。

- 现在学完之后，陆爻齐之后至少会有以下的变化
1. 从前只会 merge，现在为了保持线性的提交历史，会多多地使用 rebase 命令；
2. 对于提交的更改，学会使用交互式 rebase 与 --amend 相结合，以及 cherry-pick；
3. 对于版本的发布，会在本地上自己用 tag 标注好，并结合自动化工作流，而不是只依赖 github 的图形化；

- 总之十分推荐没有玩过这个网站的人尝试一下，以及如果你没有在本地上用过 git，也十分推荐你用一下，作为一个版本管理工具，除了学习成本较高外，并没有什么缺点。

# 证明：）
- ![Learn_Git_Branching_Finish_1](\img\LearnGitBranching\learn_git_branching_finish_1.png)

- ![Learn_Git_Branching_Finish_2](\img\LearnGitBranching\learn_git_branching_finish_2.png)

