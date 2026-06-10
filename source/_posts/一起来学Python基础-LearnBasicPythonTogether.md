---
title: 一起来学Python基础|LearnBasicPythonTogether
author: 陆爻齐-LuYaoQi
tags:
  - Python
  - LearnTogether
categories:
  - null
series:
  - null
hide: false
abbrlink: 299d5f4e
date: 2025-06-23 20:24:04
---
# 前言
- 发现 Python 写起来比 C++ 爽多了，以后写 Leetcode 和其他程序，只要对性能和应用场景没有特殊要求，就只用 Python 啦！（~~蛇堕力，悲~~）

- 总之，下面的内容是对学习了 《Python编程-从入门到实践》 基础部分的小结，不是那种事无巨细的笔记，而是学习中印象比较深刻的部分。（也就是对于本人比较新的部分）

- 原计划每章写一个博客来记录，发现大部分内容已经在 C++ 那边烂熟于心，只要补充 Python 特色的实现方式和小部分新特性就好。

- 由于下面内容可能涉及**练习的剧透**，所以想要尽可能地发挥这本书的学习效用的话，建议自行学习完成再看本文。

# 正文
- 首先，这是练习存放的[对应仓库地址](https://github.com/HSLix/PythonCrashCrouse_PractiseRecord/tree/main)

- 如果仔细看的话，会发现练习不全，因为有些练习陆爻齐觉着与其它练习考察重复，或者他们是陆爻齐已经十分熟悉的内容。

## 字符串去除前后空格
- 很多时候，我们有去除字符串前面或者后面的空格的需求，这个时候就可以使用下面的例子中展现的 strip 系列函数。
``` python
name = "\tWang \n Xiao\tMing  "
print(name.lstrip()) # Left
print(name.rstrip()) # Right
print(name.strip()) # Left and Right
```
- 运行结果会是：
```bash
$ python practise2.7.py
Wang 
 Xiao   Ming
        Wang
 Xiao   Ming
Wang
 Xiao   Ming
```

- 可以看到，三个结果，分别去除了左空格，右空格，和两边的空格。

## 字符串去除前后缀
- 去除前缀用 removeprefix()，比如
```
filename = 'python_notes.txt'
print(filename.removeprefix("python_"))
```

- 去除后缀则是 removeprefix()，比如
```python
filename = 'python_notes.txt'
print(filename.removesuffix(".txt"))
```

## 字符串大小写
- 直接上例子说明罢
```python
name = "Eric mask"
print(name.lower()) # 小写
print(name.upper()) # 大写
print(name.title()) # 首字母大写d
"""
eric mask
ERIC MASK
Eric Mask
"""
```

## 列表的顺序
- sort 负责对列表排序后，保存列表顺序 `sorted_list = num_list.sort()`
- sorted 对列表进行排序，但不会保存 `sorted_list = sorted(num_list)`
- reverse 会反转列表顺序并保存 `reversed_list = num_list.reverse()`

## 列表的删除
- del 根据索引删除列表元素 `del num_list[1]`
- remove 根据值删除元素 `num_list.remove(2)`，但是只会删除一个值，如果有多个这个值，需要循环删除。
- pop 默认弹出列表最后元素，参数可以设定弹出元素索引 `deleted_num = num_list.pop()`

## 单句 for 循环
- 直接分析例子：`nums = [value**3 for value in range(1, 11)]`

- 这个例子的意思是，创建一个列表，其中的元素是从 1 到 10 的立方组成。

## 测试
- 用 pytest，pip install 后，在命令行输入 pytest，就会对当前工作目录中文件以 test_ 开头的进行扫描，并执行其中以 test_ 开头的函数做测试。

- 有一个叫夹子的功能，可以为大规模测试中的一部分测试提供相同的元素用于测试。比如下面：
```python
@pytest.fixture
def employee():
    e = Employee('a', 'b', 1000)
    return e

def test_give_default_raise(employee):
    employee.give_raise()
    assert employee.year_salary == 6000

def test_give_custom_raise(employee):
    employee.give_raise(12138)
    assert employee.year_salary == 13138
```

- `@pytest.fixture`装饰的就是夹子，函数名可以作为其它测试函数的变量传入，变量内容就是夹子的返回值，与返回值变量名字无关。