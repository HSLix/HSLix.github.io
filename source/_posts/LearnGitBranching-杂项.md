---
title: 【一起来学 Git】LearnGitBranching-杂项【Learn Git Together】
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
abbrlink: 506b058c
date: 2025-05-05 11:34:56
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文-杂项
> Git 技术、技巧与贴士大集合
- 标题党喵，要是我来总结就是：1. 如何高效修改前几个commit；2. Tag；3. Describe
## 只取一个提交记录 & 提交的技巧#1 & 提交的技巧#2
- 由于前三个部分说的都是一件事，就合并力。

- 都是为了解决：如果想修改一个处于“中间”的 commit，该如何处理？

- 从 {% post_link LearnGitBranching-移动提交记录 %}可以看出，就两个方式 cherry-pick 和交互式 rebase。

- 无论哪个方式，都会遵循下面的步骤来执行（至少是过关的步骤）
1. 提取出中间那个要修改的 commit；
2. 通过命令`git commit --amend`来修改 commit 内容；
3. 按照原定顺序，编排修改后 commit 和涉及到的路程中其它 commit。

- 如果感觉上面的步骤说得比较抽象，那么把网站对应部分的关过一次就好力。
## Git Tag
- 就好比是给 commit 下一个“永久”的标签，作为重要版本或者错误修复的标记。

- `git tag v1 c1`就是给 c1（提交的哈希值）添加一个名为 v1 的标签，后续这个 v1 就当作分支指针一样用，可以`git checkout v1`。如果没有 c1 这个参数，就默认对 HEAD 正在指着的 commit 贴标签。

- 以及，虽然是使用后面的 describe 命令才发现的事情，但陆爻齐觉着放着比较好。陆爻齐在某个本地仓库实验 describe 命令时，出现了下面的语句提示
```
$ git describe
fatal: No annotated tags can describe '0f5137947385b1dd89d72a9b68a108b73e81d110'.
```
- 经查阅，该语句的意思是，没有带标注的 tag 可以 describe，之所以会出现这样的情况，是因为之前的 tag 的产生都是在 GitHub 发布 release 时产生的，没有标注。
> https://stackoverflow.org.cn/questions/10268641

- 平常打标签时，尽量打带标注的标签，`git tag -a "tag_name" -m "description of tag"`。

- 实际上，如果采用命令`git describe --tags`也可以正常描述无标注的标签。
## Git Describe
- `git describe`用于描述当前 HEAD 与上一个标签的情况，比如`V3.1.1-8-g0f51379`的意思是：上一个标签是叫 V3.1.1，距离是八个 commit，该 tag 的 commit 的哈希值为 g0f51379。

- 当然如果给后面加点参数会更灵活，比如`git describe main`就会描述 main 前面 tag 的情况，以及上面的`git describe --tags`就可以描述无标注标签的情况。

- 以及，如果当前的 commit 就是最新带 tag 的，describe 命令就会直接输入 tag 的名字。

