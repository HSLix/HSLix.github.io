---
title: 【一起来学 Git】LearnGitBranching-Push&Pull-Git远程仓库【Learn Git Together】
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
abbrlink: 2867501f
date: 2025-05-07 10:40:22
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文
> 是时候分享你的代码了，让编码变得社交化吧
- 事实上，远程仓库的部分才是大部分人使用 Git 的用途。对于自己的本地小项目，多数人倾向于采用比较“简单”的本地复制文件存档。只要这个项目还在开发迭代，过不了多久就会发生版本混乱。

## Git Clone
- 正如网站中所说，网站演示效果与实际不符，输入`git clone`命令后，并非从远程创建本地复制，而是在本地创建一个远程仓库的副本。

- 这个命令用得可太多了，陆爻齐需要本地复现别人的模型，或者试下别人的开源代码，都需要用 clone 复制仓库副本。（陆爻齐的LALC每天也有不少 clone 的请求啊）

## 远程分支
- 一言以蔽之，就是本地中上一次与远程仓库通信时的状态，该分支是可以用 HEAD 切换上去，不过会自动进入分离 HEAD 的状态，毕竟人工修改本地中远程仓库的状态没有且容易混乱。

- 举一个小栗子更能帮助理解该部分罢。比如，现在直接从一个仓库 clone 下来，你的分支上除了 main，其实还有 origin/main，这个 origin 是 git 给远程仓库起的别名（网站用 o 代替）。

- origin/main 只会在远程仓库更新后，与远程仓库交互时才会跟着更新，比如`git pull`,`git push`等等。

## Git Fetch
- 该命令的作用是，从远程仓库获取数据，让本地的远程分支同步更新一下。具体而言，会从远程仓库下载本地缺失的提交记录和更新远程分支指针。

- **但是**，更新远程分支不等于更新本地分支，所以使用该命令后，你会发现本地仓库的内容一点没变。

- 当然，这个时候通过以前学过的移动分支指针的知识（{% post_link LearnGitBranching-基础篇 %}中的 merge、rebase 和 {% post_link LearnGitBranching-移动提交记录 %}中的 cherry-pick），就可以实现更新了。比如`git fetch; git rebase origin/main`, `git fetch; git merge origin/main`。

- 这个命令存在感很低，至少在陆爻齐十分有限的代码实践环节里没有用过。毕竟从上面几段可以看出，这个命令对应的需求十分常见，而实现需求的过程居然需要两步，这样的麻烦促使后面一个强大命令的诞生`git pull`。

## Git Pull
- 面对`git fetch`的窘迫，`git pull`应运而生。

- 普通的`git pull`对应`git fetch; git merge origin/main`，而`git pull --rebase`对应`git fetch; git rebase origin/main`.

# 模拟团队合作
- 网站给了一个模拟其他人提交的命令，和 Git 关系不大，故不做详解。

- 本关只要模拟提交后，pull就行。

# Git Push
- 该命令的本质，是把本地的变更上传到远程仓库，同时让本地的远程分支也同步到最新的提交。

- 这个也是核心命令。一般来说，add、commit、push，三件套不会有什么太大的问题（本地的小玩具项目上），不过要真正实现网络远程协作开发，还是远远不够，当然后面会有的：）

## 偏离的工作
- 这个标题有些迷惑，要理解这个部分的内容还得看例子。

- 比如说，远程仓库本来最新提交是 c1，现在 A 同学 clone 下来，做了变更作为新提交 c2，但没有立即 push 上去。此时，B 同学 clone 了仓库，开发并 push 了提交 c3，使得远程仓库的最新提交为 c3。接下来，问题来了，A 同学可以把自己的变更 c2 push 上去吗？

- 答案是不行，毕竟 c2 是基于 c1 开发的，而仓库最新的提交是 c3，push 不能直接把 c2 连接到 c3 后，也不会自动从 c1 分离出一条新的分支，无论 A 还是 B 都是用 main 分支。

- 而要解决这个事情有两个方法，merge或rebase，比如用`git pull`把远程分支拉下来就算是 merge，或者`git pull --rebase`也能拉下来，不过不会产生merge的交叉，而是rebase。

## 锁定的Main
- 拓展下标题，应该是：“解决因锁定的Main而无法直接Push的问题”

- 锁定Main在大公司的Git流程应该属于正常范畴，毕竟如果每个人都能push到 main 那可就混沌咯。

- 所以个人只能在自己的 branch 上开发，然后 push 上去，提 pr 来等待合并。

- 但是如果发生了自己不小心在本地的 main 上提交导致无法 push 上远程仓库的情况呢？那就是本关的内容，其实只要把 main 分支指针移到和远程仓库一样的状态就行。
