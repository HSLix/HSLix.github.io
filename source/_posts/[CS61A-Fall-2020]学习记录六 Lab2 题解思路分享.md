---
title: 【CS61A-Fall-2020】学习记录六 Lab2 题解思路分享
tags: [Python, CS61A]
index_img: /img/index/CS61A.png
date: 2024-08-13 20:08:00
---
## 前言
观前提示，笔者写的代码答案放在github仓库中，此处仅记录过程与心得。

## 正文
### Q1: WWPD: Lambda the Free

有两个问题有点意思。

#### lambda 与参数
```
>>> b = lambda x: lambda: x  # Lambdas can return other lambdas!
>>> c = b(88)
>>> c
______
```
该问题看似只是普通的 lambda 中传 x，c 还传入了 x 参数值为 88

所以答案是 88 吗？

当然不是，这个问题结合下面这个问题就很显然了。
```
>>> c()
______
```
那么想清楚这个问题的关键，就在于能否分辨 c 和 c() 的含义。

把 88 代入 c 的 lambda 式子，可以换成

c = lambda : 88

本质上，c 其实还是个 lambda 式子，只不过无参数，所以直接调用 c 不会得到 88，还得象征性地传入无参数调用才行。

#### print 与 lambda
```
>>> print_lambda = lambda z: print(z)  # When is the return expression of a lambda expression executed?
>>> one_thousand = print_lambda(1000)
______

>>> one_thousand
______
```

这个 print_lambda 只是一个把传入参数直接 print 出来的函数罢了，那么第一个空当然是 1000 啦。

嗯嗯，对，不过重点是第二个空的答案，这个 print_lambda 到底把什么赋予给 one_thousand 了？

有同学会说，不是 1000 吗？

不是哟，之所以第一个空会是 1000，是因为在赋值的过程前，先执行尽右侧函数时，print 的。

排除了 1000，这又不是 Function（print_lambda 才是 Function，代入了参数后就不是啦），不是 Error，排除了所有可能，只能是 None 了。

想想也是，print 也没有返回，None 也是理所应当。

### Q2: WWPD: Higher Order Functions
值得注意的就一个点

如果有 return 而无变量承接，那 return * 就会相当于 print(*)。

### Q3: Lambdas and Currying
需要通过多个 () 调用，说明内部有几层 def。

### Q4: Count van Count
要仔细看好，在测试里面，输入的 condition 的输入参数情况如何，输出如何。

### Q5: Make Adder
不难，题目也说了，不会就放这个 [视觉化网站](https://pythontutor.com/cp/composingprograms.html#mode=edit) 看看结果

### Q6: Lambda the Environment Diagram
同 Q5

### Q7: Composite Identity Function
本质就是让你写个函数，返回对于 n ，先 f 后 g 与 先 g 后 f 是否相等，同时用上题目里的 compose1 函数。

### Q8: I Heard You Liked Functions...Q8
这个问题有点意思，题目本质是要求给个函数，可以自定义循环对一个数字按顺序循环执行的次数和数字大小。

陆爻齐的思路是，先写一个可以根据循环次数，返回应该执行函数的函数，比如 i == 1 时，返回 f1，i == 4 时，也返回 f1 这样。暂且叫 get_todo。

然后写个循环，i <= n，对数字 num，代入执行 n 次 get_todo(i) 函数，结束。

## 小结
听完课可以锻炼英语听力，同时也对这个 higher order function 有了更深的感触，期待后续课程：）