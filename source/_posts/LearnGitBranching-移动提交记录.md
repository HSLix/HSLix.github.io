---
title: 【一起来学 Git】LearnGitBranching-移动提交记录【Learn Git Together】
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
abbrlink: b3c82686
date: 2025-05-02 18:40:31
---

# 前言
- 这是一个陆爻齐跟着 `Learn Git Branching` 网站学习(~~复习~~) Git 的系列记录，会跟着其内容的步伐做学习记录，并结合自身**浅薄**的知识积累和**几乎为零**的实践经验做**一点点**的补充。
> https://learngitbranching.js.org

- 私以为，该网站比较适合有一点 Git 基础来学习，如果是完全零基础，还是看看 Git 官网教程，在 GitHub 这样的代码托管网站走一遍流程比较好。

- 注意，由于下面的笔记不可避免地涉及到过关的答案，所以**强烈建议**，自行体验过网站内容再看本文。

# 正文
> 自由修改提交树
- 作者在这表示之前的内容（{% post_link LearnGitBranching-基础篇 %}、{% post_link LearnGitBranching-高级篇 %}）已经涵盖了 Git 90% 的功能，但是剩下 10% 的功能正是为一些复杂的工作而准备的。
## Git Cherry-pick
- 正如本篇的引语一般可以自由地修改提交树（的一点），可以把任意 commit 集中到当前分支上来，比如在其他分支有名为 c2、c5 的分支（这里的名指的是哈希值），那么可以用以下命令来实现集中 c2 和 c5 到当前分支来。`git cherry-pick c2 c5`

- 非常地简单啊，还是赶紧到下一个环节罢。

## 交互式 rebase
- 此前在 {% post_link LearnGitBranching-高级篇 %}，我们学过 git rebase，这里用到了其中的参数，-i，也就是使其能够交互的原因。

- 比如我要对包括新 commit 在内，HEAD的前四个 commit 做修改，那么就用命令`git rebase -i HEAD~4`。在修改完后，会在前面第五个 commit 出现另一条分支，把修改后的四个 commit 复制到这个小分支上。

- 修改的具体过程在网站采用了另一种形式表达，并支持删除和重新排序。下面陆爻齐复制一下真实情况下，对某个项目采用这个交互式 rebase 出现的操作界面。
```
pick f8a55e9 [3.1.4]update themepack config
pick e78d15b [3.1.5]1. rewrite i18n to avoid having to update it frequently; 2. add style refresh when cost enough; 3. avoid the ego gift about ego resource
pick ec004ff [3.1.6]1. improve the logic when acquiring the ego; 2. stop when finishing mirror without enough enkephalin modules; 3. only click enhanceable ego in the enhancing
pick 0f51379 [3.1.7]update version

# Rebase 1725f2a..0f51379 onto 1725f2a (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

- 可以看到除了删除和重新排序的功能外，还支持合并 commit 等功能，反正十分强大。修改后只要保存这个命令就行。
