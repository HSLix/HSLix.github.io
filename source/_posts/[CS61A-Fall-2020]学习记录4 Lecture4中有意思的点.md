---
title: 【CS61A-Fall-2020】学习记录四 Lecture4中有意思的点
tags:
  - Python
  - CS61A
index_img: /img/index/CS61A.png
abbrlink: 3308aad7
date: 2024-02-29 21:27:00
---
## 前言
观前提示，笔者写的代码答案放在 [GitHub仓库](https://github.com/HSLix/CS61A-Fall-2020) 中，此处仅记录过程与心得。

此外，请最好在尝试独立完成该任务后再看本文，否则就很可能失去了体验本项目精华的机会

## 正文
### assert
课堂中在讲授函数，如
```
from math import pi

def area_circle(r):
    return r * r * pi
```
但老师提出，当r为-10时，函数不会报错，于是引入assert来检测参数
```
from math import pi

def area_circle(r):
    # 参数应为正数
    assert r > 0, 'A length must be positive'
    return r * r * pi
assert r > 0
```
也就是当r大于0时，无事发生；否则发生错误并提示后面的语句

 

### Higher-Order Functions
一开始读题并不懂，什么是高等级的函数呢？课上有个例子大致如下
```
def make_adder(k):
    def adder(n):
        return n + k
    return adder
```
make_adder函数内又定义了个adder函数

此时，如果调用make_adder(1)，会返回一个k为1的adder函数

而如果调用make_adder(1)(2)，则相当于调用adder(2)，此时k=1

像这样的方式可以十分方便地减少重复代码，分离函数功能，更接近DRY(Don't Repeat Yourself)

 

### Lambda
square = lambda x: x * x
lambda表达式使得简单函数无需用def定义后再调用

在python中，与def相比，lambda定义出的函数有一个特点，那就是没有内在自己的名字

直接输入上式， square，会发现在内存中，square对应的函数其实是lambda，不叫square

老师表示该功能在python中不常用，但其他有的地方会很重要，故学习并记录

下面是一个稍微有些难度的例子，与lambda本身关系不大
```
# 寻找到f值为True的正数x
def search(f):
    x = 0
    while True:
        if f(x):
            return x
        x += 1

def square(x):
    return x * x

# 可以求出y对应的x满足f（x） == y
def inverse(f):
    return lambda y: search(lambda x, f(x) == y)
```
这里的inverse函数在初次看的时候有些令人费解，但一点点看

首先从输出结果
```
>>> sqrt = inverse(square)
>>> square(8)
64
>>> sqrt(64)
8
```
可见sqrt通过inverse承担了解平方的功能

inverse中f对应的是square，而在search函数中的f对应的是lambda x, f(x) == y

sqrt(64)的64则是赋给了y，然后再在search中寻找让f(x) == y成立的正数x并返回

 

If的另一种表达
直接举栗子比较清晰

x = 0
x = 1 if x > 0 else x = -1
x > 0是条件，满足该条件时执行if前，否则执行else后

当作拓展知识吧

 