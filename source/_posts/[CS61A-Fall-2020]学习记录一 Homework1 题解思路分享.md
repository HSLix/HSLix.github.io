---
title: 【CS61A-Fall-2020】学习记录一 Homework1 题解思路分享
tags: [Python, CS61A]
index_img: /img/index/fluid_default.png
date: 2024-02-29 19:27:00
---
## 前言
观前提示，笔者写的代码答案放在github仓库中，此处仅记录过程与心得

## 正文
首先来看下hw的第一道题
### Q2: A Plus Abs B
Fill in the blanks in the following function for adding a to the absolute value of b, without calling abs. You may not modify any of the provided code other than the two blanks.
```
from operator import add, sub

def a_plus_abs_b(a, b):
    """Return a+abs(b), but without calling abs.

    >>> a_plus_abs_b(2, 3)
    5
    >>> a_plus_abs_b(2, -3)
    5
    >>> # a check that you didn't change the return statement!
    >>> import inspect, re
    >>> re.findall(r'^\s*(return .*)', inspect.getsource(a_plus_abs_b), re.M)
    ['return f(a, b)']
    """
    if b < 0:
        f = _____
    else:
        f = _____
    return f(a, b)
```
题目大意，赋予f一个函数满足在不使用abs函数的情况下，实现a+abs（b）的效果

解题过程，刚开始没注意到是赋予函数，于是分别填了a - b和a + b的错误答案，看了报错类型才反应过来，然后想起python可以函数赋予，于是将sub和add分别赋予f即可

 

### Q3: Two of Three
Write a function that takes three positive numbers as arguments and returns the sum of the squares of the two smallest numbers. Use only a single line for the body of the function.
```
def two_of_three(x, y, z):
    """Return a*a + b*b, where a and b are the two smallest members of the
    positive numbers x, y, and z.

    >>> two_of_three(1, 2, 3)
    5
    >>> two_of_three(5, 3, 1)
    10
    >>> two_of_three(10, 2, 8)
    68
    >>> two_of_three(5, 5, 5)
    50
    >>> # check that your code consists of nothing but an expression (this docstring)
    >>> # a return statement
    >>> import inspect, ast
    >>> [type(x).__name__ for x in ast.parse(inspect.getsource(two_of_three)).body[0].body]
    ['Expr', 'Return']
    """
    return _____
```
题目大意，返回三个参数中最小两个的平方和

解题过程，刚开始想了会如何在一行里选出三个参数中最小两个，然后马上想到全部一起平方加起来再减去最大的平方不就行了吗

 

### Q4: Largest Factor
Write a function that takes an integer n that is greater than 1 and returns the largest integer that is smaller than n and evenly divides n.
```
def largest_factor(n):
    """Return the largest factor of n that is smaller than n.

    >>> largest_factor(15) # factors are 1, 3, 5
    5
    >>> largest_factor(80) # factors are 1, 2, 4, 5, 8, 10, 16, 20, 40
    40
    >>> largest_factor(13) # factor is 1 since 13 is prime
    1
    """
    "*** YOUR CODE HERE ***"
```
题目大意，找到比整数n小的最大的能整除n的数字

解题过程，直接循环，从1开始穷举，可以对循环条件除2或取根号2来减少循环次数

 

### Q5: If Function vs Statement
Let's try to write a function that does the same thing as an if statement.
```
def if_function(condition, true_result, false_result):
    """Return true_result if condition is a true value, and
    false_result otherwise.

    >>> if_function(True, 2, 3)
    2
    >>> if_function(False, 2, 3)
    3
    >>> if_function(3==2, 3+2, 3-2)
    1
    >>> if_function(3>2, 3+2, 3-2)
    5
    """
    if condition:
        return true_result
    else:
        return false_result
```
Despite the doctests above, this function actually does not do the same thing as an if statement in all cases. To prove this fact, write functions cond, true_func, and false_func such that with_if_statement prints the number 47, but with_if_function prints both 42 and 47.
```
def with_if_statement():
    """
    >>> result = with_if_statement()
    47
    >>> print(result)
    None
    """
    if cond():
        return true_func()
    else:
        return false_func()

def with_if_function():
    """
    >>> result = with_if_function()
    42
    47
    >>> print(result)
    None
    """
    return if_function(cond(), true_func(), false_func())

def cond():
    "*** YOUR CODE HERE ***"

def true_func():
    "*** YOUR CODE HERE ***"

def false_func():
    "*** YOUR CODE HERE ***"
```
题目大意，通过编辑三个函数cond(),true_func(),false_func()，使得with_if_statement和with_if_fuction()产生预想的结果

解题过程，关键之关键在于了解到python中，将函数作为参数时，会先将函数运行，再将其结果作为参数返回，因此填上对应的数字，并调好条件cond就能成功解题

 

### Q6: Hailstone
Douglas Hofstadter's Pulitzer-prize-winning book, Gödel, Escher, Bach, poses the following mathematical puzzle.

Pick a positive integer n as the start.
If n is even, divide it by 2.
If n is odd, multiply it by 3 and add 1.
Continue this process until n is 1.
The number n will travel up and down but eventually end at 1 (at least for all numbers that have ever been tried -- nobody has ever proved that the sequence will terminate). Analogously, a hailstone travels up and down in the atmosphere before eventually landing on earth.

Breaking News (or at least the closest thing to that in math). There was a recent development in the hailstone conjecture last year that shows that almost all numbers will eventually get to 1 if you repeat this process. This isn't a complete proof but a major breakthrough.

This sequence of values of n is often called a Hailstone sequence. Write a function that takes a single argument with formal parameter name n, prints out the hailstone sequence starting at n, and returns the number of steps in the sequence:
```
def hailstone(n):
    """Print the hailstone sequence starting at n and return its
    length.

    >>> a = hailstone(10)
    10
    5
    16
    8
    4
    2
    1
    >>> a
    7
    """
    "*** YOUR CODE HERE ***"
```
题目大意，有一种数学规律（尚未完全证明），对一个正整数，若其为偶数，则除2，若其为奇数，则乘3并加1，重复上述步骤，此数定终为1。现有n，输出n经上述规律到1的过程，并返回计算次数

解题过程，写循环，循环条件是数为1，每次循环输出该数，再设一个变量每次循环++即可

 

## 小结
虽然题目不难，但仍发现了自己一些问题，比如审题不清，有一定的原因是题目是英语而非中文，不过都要适应嘛