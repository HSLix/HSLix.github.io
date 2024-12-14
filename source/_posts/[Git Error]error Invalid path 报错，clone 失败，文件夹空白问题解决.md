---
title: 【Git Error】error Invalid path 报错，clone 失败，文件夹空白问题解决
tags:
  - Git
index_img: /img/index/Git.png
abbrlink: 7f94f797
date: 2024-05-04 14:48:00
---
# 问题背景
可能是在 clone 某仓库后出现形如下面报错
```
$ git clone https://github.com/*.git
Cloning into '*'...
remote: Enumerating objects: *, done.
remote: Counting objects: 100% (*/*), done.
remote: Compressing objects: 100% (*/*), done.
remote: Total * (delta *), reused * (delta *), pack-reused *
Receiving objects: 100% (*/*), * MiB | 8.40 MiB/s, done.
Resolving deltas: 100% (*/*), done.
error: invalid path '*.md'
fatal: unable to checkout working tree
warning: Clone succeeded, but checkout failed.
You can inspect what was checked out with 'git status'
and retry with 'git restore --source=HEAD :/'
```
而且仓库里除了.git文件夹就“一尘不染”。

# 解决方法
在仓库的文件夹中打开终端，并输入
`git config core.protectNTFS false`
大致意思是关闭文件保护之类的，然后切换为刚刚的分支，如
`git checkout main`
然后文件夹就正常咯

# 后记
一个简单记录，并非笔者原创的方法，只是想以自己的方式记录该方法。

# 参考网址
https://www.cnblogs.com/zackary/p/13702643.html