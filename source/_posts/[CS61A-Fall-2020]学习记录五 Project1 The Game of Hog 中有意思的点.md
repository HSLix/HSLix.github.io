---
title: 【CS61A-Fall-2020】学习记录五 Project1 The Game of Hog 中有意思的点
tags: [Python, CS61A]
index_img: /img/index/fluid_default.png
date: 2024-07-18 00:58:00
---
首先，本文不是总结归纳，只是记录一些有趣的知识点罢了

此外，观前提示，请最好在尝试独立完成该任务后再看本文，否则就很可能失去了体验本项目精华的机会

## 函数参数前的*
在项目原文件中的dice.py 里的 make_test_dice函数采用这样的方式传参
```
def make_test_dice(*outcomes):
  # 省略内容
  pass
```

我记得python中没像c那样的指针，于是查询资料，小结如下
函数参数前一颗*会识别为元组，两颗则识别为字典

比如我要是传 make_test_dice(1, 2, 4, 3)，outcomes就是元组(1, 2, 4, 3)

举一个稍微复合一点点的例子
```
def foo(a, b=10, *args, **kwargs):
    print (a)
    print (b)
    print (args)
    print (kwargs)

foo(1, 2, 3, 4, e=5, f=6, g=7)
```

上述例子来自菜鸟教程，运行结果为
```
1
2
(3, 4)
{'e': 5, 'f': 6, 'g':7}
```

可发现除了定好的a、b，c和d都纳入元组中，而efg有=赋值，作为字典看待

上面的例子都发生在定义函数参数列表，如果在调用时有*说明什么呢？

再看一个例子
```
def func(num1, num2):
  print(num1, num2)

num = [1, 2]
func(*num)
```

运行结果为
```
1 2
```

一个参数变两，*在这里起到了解压参数列表的作用
但笔者尚未想到该功能应用的方便之处

##  Higher Order Function 高阶函数
这是一个让陆爻齐觉得十分精妙的功能，以函数为参数并返回函数，从 C 初学者的角度来说，太抽象了

就是在 Problem 7 的答案代码

```

def announce_highest(who, last_score=0, running_high=0):
    """Return a commentary function that announces when WHO's score
    increases by more than ever before in the game.

    NOTE: the following game is not possible under the rules, it's just
    an example for the sake of the doctest

    >>> f0 = announce_highest(1) # Only announce Player 1 score gains
    >>> f1 = f0(12, 0)
    >>> f2 = f1(12, 9)
    9 point(s)! The most yet for Player 1
    >>> f3 = f2(20, 9)
    >>> f4 = f3(20, 30)
    21 point(s)! The most yet for Player 1
    >>> f5 = f4(20, 47) # Player 1 gets 17 points; not enough for a new high
    >>> f6 = f5(21, 47)
    >>> f7 = f6(21, 77)
    30 point(s)! The most yet for Player 1
    """
    assert who == 0 or who == 1, 'The who argument should indicate a player.'
    # BEGIN PROBLEM 7
    "*** YOUR CODE HERE ***"
    def say(new_score_0, new_score_1, last_running_high = running_high):
        if who == 0:
            new_score = new_score_0
            new_running_high = new_score_0 - last_score
        else:
            new_score = new_score_1
            new_running_high = new_score_1 - last_score

        if new_running_high > last_running_high:
            last_running_high = new_running_high
            print(str(new_running_high),"point(s)! The most yet for Player " + str(who))
        
        new_run_high = last_running_high
        
        return announce_highest(who, new_score, new_run_high)
        
    return say
    # END PROBLEM 7
```

这段代码就是记录玩家 1 或 2 分数变化的幅度，并在最大幅度记录更新时 print 的函数。

传统函数（指 C/C++ 这种）要实现这种记录更新，必须在外部存储，用类或者其它外部变量什么的，因为函数一旦执行完毕，内部空间将全部释放。

下面对该代码做点拆解，忽略 say 的内容、断言、测试和注释，可看成

```

def announce_highest(who, last_score=0, running_high=0):
    # BEGIN PROBLEM 7
    "*** YOUR CODE HERE ***"
    def say(new_score_0, new_score_1, last_running_high = running_high):
        pass
        
    return say
    # END PROBLEM 7
```

可见，调用 announce_highest 的本质是获取到一个 say 函数，每次调用 say 函数，我们都期待它能检测是否要更新记录，而不断地更新最大幅度，则要不断地获取并调用 say 函数，那让 say 返回获取 say 函数的函数就解决了这个问题，即下面代码

```

def announce_highest(who, last_score=0, running_high=0):
    # BEGIN PROBLEM 7
    "*** YOUR CODE HERE ***"
    def say(new_score_0, new_score_1, last_running_high = running_high):
 
        pass

        return announce_highest(who, new_score, new_run_high)
        
    return say
    # END PROBLEM 7
```

再看测试代码就清晰多了


``>>> f0 = announce_highest(1) # Only announce Player 1 score gains`` 这里的 f0 实际上是 who 为 1 时的 say 函数


``>>> f1 = f0(12, 0)`` f1 的本质是代入 new_score_0 = 12 和 new_score_1 = 0 的 say 函数执行后，返回的新的 say 函数。你问 announce_highest 去哪了？announce_highest 也返回的 say 函数，只是没有参数代入的 say 函数而已

其它测试语句以上述类推

后面在 [geek for geek](https://www.geeksforgeeks.org/higher-order-functions-in-python/) 进行了更深入的学习，发现此前在 LALC 用的迭代器就是高阶函数的运用