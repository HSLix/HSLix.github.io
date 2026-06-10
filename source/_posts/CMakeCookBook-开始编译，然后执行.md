---
title: 【一起来学 CMake】CMakeCookBook-开始编译，然后执行【Learn CMake Together】
author: 陆爻齐-LuYaoQi
cover: /img/index/CMake.png
tags:
  - CMake
  - LearnTogether
categories:
  - 一起来学 CMake | Learn CMake Together
series:
  - 一起来学 CMake | Learn CMake Together
hide: false
abbrlink: 5cb682ce
date: 2025-05-21 15:53:02
---

# 前言
- 这是一个陆爻齐主要借助资源 [`CMake Cookbook`](https://chenxiaowei.gitbook.io/cmake-cookbook) 学习 CMake 的系列记录。
> https://chenxiaowei.gitbook.io/cmake-cookbook

- 不定时更新，也不一定全按上面的章节顺序走，用到哪学到哪罢了：）

- 运行环境：VSCode + CMake + MinGW

# 正文
- 首先在项目新建一个名为`hello.cpp`的文件，内容是
```c++
#include <iostream>

using namespace std;

int main() {
  cout << "Hello World!" << endl;

  return 0;
}
```
- 上面是十分甚至有九分标准的代码，要将这个代码编译执行， 在IDE 上就是点一下或者两下按钮的事，但在 VSCode，这事没那么轻松。

- 下载完 C++ 之类的拓展后，虽然右上角出现了运行和调试的按钮，但对多文件不管用，这是因为 VSCode 还需要你编辑 `.vscode` 文件夹内的 json 配置文件，麻烦，而且与其它编辑器不通用。虽然现在也不用其它编辑器，但学习一种更通用的方法总是好的，这就是本系列的由来。

## 对 Hello World！单文件编译与执行
> https://chenxiaowei.gitbook.io/cmake-cookbook/1.0-chinese/1.1-chinese
- 那么进入正题，如何用 CMake 来对单文件编译然后执行呢？

### 编译步骤
- 首先要初始化 CMake 的环境，先在项目的根目录下新建一个 CMakeLists.txt 文件，内容只需要三行。
```cmake
# 第一句的意思是要求当前 CMake 的版本不低于 3.5，否则就会报错
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
# 这句话的意思，是为当前项目起名为 hello，同时限定语言为 cpp
project(hello LANGUAGES CXX)
# 这句话的意思是，把 hello.cpp 文件编译为名字是 hello-world 的可执行文件
add_executable(hello-world hello.cpp)
```

- 然后创建 build 目录来配置项目，用命令有以下三行：
``` shell
mkdir build
cd build
cmake ..
```

- 注意，要是这时没有`CMakeLists.txt`文件的话，是不会配置成功的。

- 配置文件后，下面这句代码才会正式开始编译工作，注意，输入命令的位置在 build 文件夹内：`cmake --build .`

### 可执行文件位置
- 这时就有人说了：“陆爻齐！我看到编译成功了，但没有执行程序，你是不是教错了？”，嘛，编译成功并不是执行成功，在 build 文件夹内找找可执行文件运行就可以啦。

- 再次注意，根据生成器的不同，可执行文件的位置也会有所不同。

- 比如要是默认生成器是`Visual Studio`系列的话，build 文件夹内很可能会有个 Debug 文件夹，里面就是可执行文件；如果是`MinGW`的话，可执行文件会直接出现在 build 文件夹内。

- 刚刚说生成器的不同会对编译有所影响，而生成器当然也是可以切换的，这将在后文阐述。

### 更简单的编译步骤
- 上述的编译步骤其实是“传统派”，CMake 支持更为“简单”的方法。

- 就是在 CMakeLists 文件写好后，在根目录运行 `cmake -H. -Bbuild`，`-H.`表示当前目录中搜索根CMakeLists.txt文件。`-Bbuild`告诉CMake在一个名为build的目录中生成所有的文件。

- 嘛，陆爻齐本人姑且还是传统派的：）所以后续提起编译步骤就默认是传统方法吧。

### build文件夹的意义
- 回到编译步骤中写完 CMakeLists 的时候，如果此时直接运行命令`cmake .`也可以完成编译工作并正常运行。

- **但是**，那些繁杂的配置文件就会出现在项目根目录，十分不整齐，这就需要 build 文件夹装好这些配置文件。

## 切换生成器
> https://chenxiaowei.gitbook.io/cmake-cookbook/1.0-chinese/1.2-chinese
- 回到编译步骤中写完 CMakeLists 的时候，先用`cmake --help`来查看自己能选什么生成器。

- 比如，我想换 MinGW Makefiles，在 build 文件夹中这么来初始化：`cmake -G "MinGW Makefiles" ..`

- 注意，如果已经初始化了，是不能直接用上面的句子重新初始化，需要清除冲突部分。可以干脆删掉 build 文件夹，或者专门清理特定的缓存文件夹。

## 多文件编译与执行
> https://chenxiaowei.gitbook.io/cmake-cookbook/1.0-chinese/1.3-chinese
- 题目都说是多文件了，项目的内容也要有所变化，不过这部分陆爻齐没什么需要特别补充的，建议直接看[链接](https://chenxiaowei.gitbook.io/cmake-cookbook/1.0-chinese/1.3-chinese)完成初始配置。

- 然后就在前面单文件的基础上改改 CMakeLists 就行，直接看代码吧，说明都在注释里
```cmake
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project(hello LANGUAGES CXX)
# 把新增的两个 Message 编成静态库 message
add_library(message STATIC Message.hpp Message.cpp)
add_executable(hello-world hello.cpp)
# 把 message 库连接到可执行文件上
target_link_libraries(hello-world message)
```

- 目前暂且只对`STATIC`感兴趣，那就说明下：STATIC 是静态库，SHARED 是动态库，或者说是动态共享对象（DSO）。