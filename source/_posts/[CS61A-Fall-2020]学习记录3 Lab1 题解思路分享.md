---
title: 【CS61A-Fall-2020】学习记录三 Lab1 题解思路分享
tags:
  - Python
  - CS61A
index_img: /img/index/CS61A.png
abbrlink: 83eef88b
date: 2024-02-29 19:30:00
---
## 前言
观前提示，笔者写的代码答案放在 [GitHub仓库](https://github.com/HSLix/CS61A-Fall-2020) 中，此处仅记录过程与心得。

此外，请最好在尝试独立完成该任务后再看本文，否则就很可能失去了体验本项目精华的机会

## 正文
### Q1: WWPD: Control
### Q2: WWPD: Veritasiness
### Q3: Debugging Quiz!
前三问分别问函数执行结果，python中布尔运算结果，程序报错最可能情况

所以就摘录部分令我印象深刻的知识点也就是做错的（悲

复制代码
```
>>> def how_big(x):
...     if x > 10:
...         print('huge')
...     elif x > 5:
...         return 'big'
...     elif x > 0:
...         print('small')
...     else:
...         print("nothin")
>>> how_big(7)
```

“这题好简单啊，有什么难的吗？不就是big？”

是big，也不是

仔细看，elif x>5: 下面是return 'big'不是print

所以答案是'big'

 

而对于布尔运算则有两种代表情况
```
>>> 1 and 3 and 6 and 10 and 15
```
在结果为对的布尔运算中，返回最后对的部分，

所以这题答案为15

而结果为错的布尔运算中，则返回第一个错的部分

 

### Q4: Falling Factorial
Let's write a function falling, which is a "falling" factorial that takes two arguments, n and k, and returns the product of k consecutive numbers, starting from n and working downwards.

```
def falling(n, k):
    """Compute the falling factorial of n to depth k.

    >>> falling(6, 3)  # 6 * 5 * 4
    120
    >>> falling(4, 3)  # 4 * 3 * 2
    24
    >>> falling(4, 1)  # 4
    4
    >>> falling(4, 0)
    1
    """
    "*** YOUR CODE HERE ***"
```
题目大意，接收两参数n，k，执行k次，从n向下递减数连乘，并规定乘0次的结果为1

解题过程，先定个result=1，并返回result，然后以k>0为条件建立循环，并在其中用n乘result，n--

 

### Q5: Sum Digits
Write a function that takes in a nonnegative integer and sums its digits. (Using floor division and modulo might be helpful here!)

```
def sum_digits(y):
    """Sum all the digits of y.

    >>> sum_digits(10) # 1 + 0 = 1
    1
    >>> sum_digits(4224) # 4 + 2 + 2 + 4 = 12
    12
    >>> sum_digits(1234567890)
    45
    >>> a = sum_digits(123) # make sure that you are using return rather than print
    >>> a
    6
    """
    "*** YOUR CODE HERE ***"
```
题目大意，输入一个正数，返回该数各位之和

解题过程，循环条件为该数大于零，不断取余并用10整除该数，再将余数相加

 

### Q6: WWPD: What If?
该题要注意的是，if如果有满足的条件，后面的elif，else不会执行，同时还要看好if和else的对应关系。

 
### Q7: Double Eights
Write a function that takes in a number and determines if the digits contain two adjacent 8s.

```
def double_eights(n):
    """Return true if n has two eights in a row.
    >>> double_eights(8)
    False
    >>> double_eights(88)
    True
    >>> double_eights(2882)
    True
    >>> double_eights(880088)
    True
    >>> double_eights(12345)
    False
    >>> double_eights(80808080)
    False
    """
    "*** YOUR CODE HERE ***"
```
题目大意，检测一个给定数字有无两个紧挨的8

解题过程，循环取余，判断数字是否为8，是则让计数器加一，计数器为2时结果为真，停止循环，最后计数器仍不为2则为假。易错点是数字非8时，记得将计数器归0

## 小结
题不难，但让我很好的领略了一些python的特点，期待后续的学习