---
title: 【CS61A-Fall-2020】学习记录二 Lecture1，2，3中有意思的点
tags: [Python, CS61A]
index_img: /img/index/CS61A.png
date: 2024-02-29 19:28:00
---
## 前言
观前提示，笔者写的代码答案放在 [GitHub仓库](https://github.com/HSLix/CS61A-Fall-2020) 中，此处仅记录过程与心得。

此外，请最好在尝试独立完成该任务后再看本文，否则就很可能失去了体验本项目精华的机会

## 正文
### doctest
好东西啊，此前的学习从未看到过，可以在函数的注释中通过特定格式便可书写文档测试，比如
```
def divide_exact(n, d):
 　'''
    Return the quotient and remainder of dividing N by D.
    >>>q, r = divide_exact(2013, 10)
    >>>q
    201
    >>> r
    3
    '''    
    return floordiv(n, d), mod(n, d)
```
假设该 python 文件命名为 file.py，此时在本文件夹打开 bash，并输入命令行

python3  file.py 就能通过 doctest 来检查，如果没有其它输出，说明测试正常

可通过 -v 来显示运行过程，

当写成python3 -m doctest -v file.py时，该命令会详细运行测试用例

此外，doctest只会在命令行这种测试环境下才会运行，不用担心会在正式使用时误触发

 

### None
与C不同，python的函数都有返回，只是一部分返回为None，举个栗子

print(3)
会输出3，那么

print(print(1), print(2))
会输出什么呢？

答案是……

```
1
2
None None
```

为什么呢？

因为python在调用函数时会先将参数计算尽，所以才会先输出1，再输出2

而后面出现的 None None 则是 print 函数返回的是 None

老师将函数大致分为两类

pure functions 和 non-pure fuctions，前者单纯返回函数值，后者除函数值有其他的操作