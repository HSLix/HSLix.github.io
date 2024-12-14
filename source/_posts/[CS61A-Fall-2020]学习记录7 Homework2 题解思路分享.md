---
title: 【CS61A-Fall-2020】学习记录七 Homework2 题解思路分享
tags:
  - Python
  - CS61A
index_img: /img/index/CS61A.png
abbrlink: 58db1e1b
date: 2024-08-28 15:22:00
---
## 前言
观前提示，笔者写的代码答案放在 [GitHub仓库](https://github.com/HSLix/CS61A-Fall-2020) 中，此处仅记录过程与心得。

此外，请最好在尝试独立完成该任务后再看本文，否则就很可能失去了体验本项目精华的机会

## Higher-Order Functions
### Q1: Product
Write a function called product that returns the product of the first n terms of a sequence. Specifically, product takes in an integer n and term, a single-argument function that determines a sequence. (That is, term(i) gives the ith term of the sequence.) product(n, term) should return term(1) * ... * term(n).
```
def product(n, term):
    """Return the product of the first n terms in a sequence.

    n: a positive integer
    term:  a function that takes one argument to produce the term

    >>> product(3, identity)  # 1 * 2 * 3
    6
    >>> product(5, identity)  # 1 * 2 * 3 * 4 * 5
    120
    >>> product(3, square)    # 1^2 * 2^2 * 3^2
    36
    >>> product(5, square)    # 1^2 * 2^2 * 3^2 * 4^2 * 5^2
    14400
    >>> product(3, increment) # (1+1) * (2+1) * (3+1)
    24
    >>> product(3, triple)    # 1*3 * 2*3 * 3*3
    162
    """
    "*** YOUR CODE HERE ***"
```

### A1
题目大意是，用一个函数，计算从 1-n 经 term 函数处理之积

嘛，用循环可以轻松解决，不过陆爻齐用了递归

### Q2: Accumulate
Let's take a look at how product is an instance of a more general function called accumulate, which we would like to implement:
```
def accumulate(fuse, start, n, term):
    """Return the result of fusing together the first n terms in a sequence 
    and start.  The terms to be fused are term(1), term(2), ..., term(n). 
    The function fuse is a two-argument commutative & associative function.

    >>> accumulate(add, 0, 5, identity)  # 0 + 1 + 2 + 3 + 4 + 5
    15
    >>> accumulate(add, 11, 5, identity) # 11 + 1 + 2 + 3 + 4 + 5
    26
    >>> accumulate(add, 11, 0, identity) # 11 (fuse is never used)
    11
    >>> accumulate(add, 11, 3, square)   # 11 + 1^2 + 2^2 + 3^2
    25
    >>> accumulate(mul, 2, 3, square)    # 2 * 1^2 * 2^2 * 3^2
    72
    >>> # 2 + (1^2 + 1) + (2^2 + 1) + (3^2 + 1)
    >>> accumulate(lambda x, y: x + y + 1, 2, 3, square)
    19
    """
    "*** YOUR CODE HERE ***"
```

accumulate has the following parameters:

fuse: a two-argument function that specifies how the current term is fused with the previously accumulated terms
start: value at which to start the accumulation
n: a non-negative integer indicating the number of terms to fuse
term: a single-argument function; term(i) is the ith term of the sequence
Implement accumulate, which fuses the first n terms of the sequence defined by term with the start value using the fuse function.

For example, the result of accumulate(add, 11, 3, square) is
```
add(11,  add(square(1), add(square(2),  square(3)))) =
    11 +     square(1) +    square(2) + square(3)    =
    11 +     1         +    4         + 9            = 25
```
Assume that fuse is commutative, fuse(a, b) == fuse(b, a), and associative, fuse(fuse(a, b), c) == fuse(a, fuse(b, c)).

Then, implement summation (from lecture) and product as one-line calls to accumulate.

Important: Both summation_using_accumulate and product_using_accumulate should be implemented with a single line of code starting with return.
```
def summation_using_accumulate(n, term):
    """Returns the sum: term(1) + ... + term(n), using accumulate.

    >>> summation_using_accumulate(5, square) # square(0) + square(1) + ... + square(4) + square(5)
    55
    >>> summation_using_accumulate(5, triple) # triple(0) + triple(1) + ... + triple(4) + triple(5)
    45
    >>> # This test checks that the body of the function is just a return statement.
    >>> import inspect, ast
    >>> [type(x).__name__ for x in ast.parse(inspect.getsource(summation_using_accumulate)).body[0].body]
    ['Expr', 'Return']
    """
    return ____

def product_using_accumulate(n, term):
    """Returns the product: term(1) * ... * term(n), using accumulate.

    >>> product_using_accumulate(4, square) # square(1) * square(2) * square(3) * square()
    576
    >>> product_using_accumulate(6, triple) # triple(1) * triple(2) * ... * triple(5) * triple(6)
    524880
    >>> # This test checks that the body of the function is just a return statement.
    >>> import inspect, ast
    >>> [type(x).__name__ for x in ast.parse(inspect.getsource(product_using_accumulate)).body[0].body]
    ['Expr', 'Return']
    """
    return ____
```
### A2
题目大意，是先完成 accumulate 函数，这是一个用 fuse 处理 start、term(1) ... term(n) 之间的函数，再用 accumulate 函数实现两个衍生的函数。

没什么难点，想说的话同 A1

### Q3: Make Repeater
Implement the function make_repeater which takes a one-argument function f and a positive integer n. It returns a one-argument function, where make_repeater(f, n)(x) returns the value of f(f(...f(x)...)) in which f is applied n times to x. For example, make_repeater(square, 3)(5) squares 5 three times and returns 390625, just like square(square(square(5))).
```
def make_repeater(f, n):
    """Returns the function that computes the nth application of f.

    >>> add_three = make_repeater(increment, 3)
    >>> add_three(5)
    8
    >>> make_repeater(triple, 5)(1) # 3 * (3 * (3 * (3 * (3 * 1))))
    243
    >>> make_repeater(square, 2)(5) # square(square(5))
    625
    >>> make_repeater(square, 3)(5) # square(square(square(5)))
    390625
    """
    "*** YOUR CODE HERE ***"
```
### A3
题目大意是，写一个函数，传入一个单参函数和数字 n，分别是处理参数的方式和次数，返回一个可传一个参数的函数。

实现方式就是内部定个函数，把传入数字，用 f 执行 n 次即可，也不难

## Recursion
### Q4: Digit Distance
For a given integer, the digit distance is the sum of the absolute differences between consecutive digits. For example:

The digit distance of 6 is 0.
The digit distance of 61 is 5, as the absolute value of 6 - 1 is 5.
The digit distance of 71253 is 12 (6 + 1 + 3 + 2).
Write a function that determines the digit distance of a given positive integer. You must use recursion or the tests will fail.

Hint: There are multiple valid ways of solving this problem! If you're stuck, try writing out an iterative solution first, and then convert your iterative solution into a recursive one.
```
def digit_distance(n):
    """Determines the digit distance of n.

    >>> digit_distance(3)
    0
    >>> digit_distance(777)
    0
    >>> digit_distance(314)
    5
    >>> digit_distance(31415926535)
    32
    >>> digit_distance(3464660003)
    16
    >>> from construct_check import check
    >>> # ban all loops
    >>> check(HW_SOURCE_FILE, 'digit_distance',
    ...       ['For', 'While'])
    True
    """
    "*** YOUR CODE HERE ***"
```
### A4
题目大意是，计算一个数字各位差绝对值之和，且不准用循环的方式。

陆爻齐的思路是，每次取第一位和第二位数字差的绝对值加上 n 整除 10 后的数字，如此便可计算出各位差绝对值，与参考答案不谋而合捏。

### Q5: Interleaved Sum
Write a function interleaved_sum, which takes in a number n and two one-argument functions: odd_func and even_func. It applies odd_func to every odd number and even_func to every even number from 1 to n inclusive and returns the sum.

For example, executing interleaved_sum(5, lambda x: x, lambda x: x * x) returns 1 + 2*2 + 3 + 4*4 + 5 = 29.

Important: Implement this function without using any loops or directly testing if a number is odd or even -- aka modulos (%) are not allowed! Instead of directly checking whether a number is even or odd, start with 1, which you know is an odd number.

Hint: Introduce an inner helper function that takes an odd number k and computes an interleaved sum from k to n (including n).
```
def interleaved_sum(n, odd_func, even_func):
    """Compute the sum odd_func(1) + even_func(2) + odd_func(3) + ..., up
    to n.

    >>> identity = lambda x: x
    >>> square = lambda x: x * x
    >>> triple = lambda x: x * 3
    >>> interleaved_sum(5, identity, square) # 1   + 2*2 + 3   + 4*4 + 5
    29
    >>> interleaved_sum(5, square, identity) # 1*1 + 2   + 3*3 + 4   + 5*5
    41
    >>> interleaved_sum(4, triple, square)   # 1*3 + 2*2 + 3*3 + 4*4
    32
    >>> interleaved_sum(4, square, triple)   # 1*1 + 2*3 + 3*3 + 4*3
    28
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'interleaved_sum', ['While', 'For', 'Mod']) # ban loops and %
    True
    >>> check(HW_SOURCE_FILE, 'interleaved_sum', ['BitAnd', 'BitOr', 'BitXor']) # ban bitwise operators, don't worry about these if you don't know what they are
    True
    """
    "*** YOUR CODE HERE ***"
```
### A5
题目大意，交错和，即对奇偶数各用奇偶的函数处理，再相加得和，不准使用循环和 %，以及位运算来禁止直接判断数字奇偶。

此题，陆爻齐用了取巧的办法越过了检查，仍然直接判断奇偶，即用递归判断，n 次递归，用另一个变量 1/0，以得出奇偶，下面是看了参考答案总结的思路。

不知 n 的奇偶，但可以从 1 推到 n，于是可写个内部函数，计算 1 2 ……，两位两位地计算和，递归，最后返回结果即可

### Q6: Count Coins
Given a positive integer total, a set of coins makes change for total if the sum of the values of the coins is total. Here we will use standard US Coin values: 1, 5, 10, 25. For example, the following sets make change for 15:

15 1-cent coins
10 1-cent, 1 5-cent coins
5 1-cent, 2 5-cent coins
5 1-cent, 1 10-cent coins
3 5-cent coins
1 5-cent, 1 10-cent coin
Thus, there are 6 ways to make change for 15. Write a recursive function count_coins that takes a positive integer total and returns the number of ways to make change for total using coins.

You can use either of the functions given to you:

next_larger_coin will return the next larger coin denomination from the input, i.e. next_larger_coin(5) is 10.
next_smaller_coin will return the next smaller coin denomination from the input, i.e. next_smaller_coin(5) is 1.
Either function will return None if the next coin value does not exist
There are two main ways in which you can approach this problem. One way uses next_larger_coin, and another uses next_smaller_coin. It is up to you which one you want to use!

Important: Use recursion; the tests will fail if you use loops.

Hint: Refer to the implementation of count_partitions for an example of how to count the ways to sum up to a final value with smaller parts. If you need to keep track of more than one value across recursive calls, consider writing a helper function.
```
def next_larger_coin(coin):
    """Returns the next larger coin in order.
    >>> next_larger_coin(1)
    5
    >>> next_larger_coin(5)
    10
    >>> next_larger_coin(10)
    25
    >>> next_larger_coin(2) # Other values return None
    """
    if coin == 1:
        return 5
    elif coin == 5:
        return 10
    elif coin == 10:
        return 25

def next_smaller_coin(coin):
    """Returns the next smaller coin in order.
    >>> next_smaller_coin(25)
    10
    >>> next_smaller_coin(10)
    5
    >>> next_smaller_coin(5)
    1
    >>> next_smaller_coin(2) # Other values return None
    """
    if coin == 25:
        return 10
    elif coin == 10:
        return 5
    elif coin == 5:
        return 1

def count_coins(total):
    """Return the number of ways to make change using coins of value of 1, 5, 10, 25.
    >>> count_coins(15)
    6
    >>> count_coins(10)
    4
    >>> count_coins(20)
    9
    >>> count_coins(100) # How many ways to make change for a dollar?
    242
    >>> count_coins(200)
    1463
    >>> from construct_check import check
    >>> # ban iteration
    >>> check(HW_SOURCE_FILE, 'count_coins', ['While', 'For'])
    True
    """
    "*** YOUR CODE HERE ***"
```
### A6
题目大意，就是将给定的数字划分成 1、5、10、25 的组合，给出组合个数，不可使用循环。可借助已写好的 next_larger_coin 等函数

此题可这么看，从一个数字向下蔓延分支，可大致分两类，一类是尝试用当前数字减去一次额度，一类是额度变化（变大变小都可），如此递归下去各种路线都可计算到。

## 小结
Q5 和 Q6 都挺有思考的趣味，递归很早就学了，但体会递归的魅力，还是在此