---
title: 【Conda】 miniconda 使用手册
tags: [Conda]
index_img: /img/index/miniconda_logo.png
date: 2024-09-16 14:14:00
---
# 前言
本文是陆爻齐平时经常使用 conda 的一些记录，且作为复习笔记罢

# 正文
## 创建环境
`conda create -n name python=3.9.*`
这条语句的意思是创建名字为 name，python 版本为 3.9 中最新的版本的环境，-n 后面跟名字，python 也可先不确定，但还没试过后面安 python。

## 查看环境
`conda env list`
会列出所有已有的虚拟环境

## 删除环境
`conda remove -n name --all`
删除名字为 name 的环境

## 克隆环境
`conda create -n name --clone other`
将名为 other 的环境复制到新环境 name 中

## 更改存储和环境的地方
[参考网址](https://www.autodl.com/docs/miniconda/#:~:text=%E5%8F%96%E6%B6%88%E8%AE%BE%E7%BD%AE%E5%AE%89%E8%A3%85%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83%E5%88%B0%E6%95%B0%E6%8D%AE%E7%9B%98,%E7%BC%96%E8%BE%91%2Froot%2F.condarc%E6%96%87%E4%BB%B6%EF%BC%8C%E5%88%A0%E9%99%A4%E5%AF%B9%E5%BA%94%E7%9A%84%E8%B7%AF%E5%BE%84%E6%89%80%E5%9C%A8%E8%A1%8C%E5%8D%B3%E5%8F%AF)
执行以下命令设置将虚拟环境安装到/root/autodl-tmp/conda/envs， 包缓存到/root/autodl-tmp/conda/pkgs
```
mkdir -p /root/autodl-tmp/conda/pkgs
conda config --add pkgs_dirs /root/autodl-tmp/conda/pkgs

mkdir -p /root/autodl-tmp/conda/envs
conda config --add envs_dirs /root/autodl-tmp/conda/envs
```

## 打包环境
参考[知乎文章](https://zhuanlan.zhihu.com/p/540615230)

构建环境的操作系统必须与目标的操作系统匹配。这意味着在Windows上构建的环境不能重新定位到Linux。

### 打包
```
# 把虚拟环境 my_env 打包为 my_env.tar.gz
conda pack -n my_env

# -o 参数指定打包路径和名称，把虚拟环境 my_env 打包为 out_name.tar.gz
conda pack -n my_env -o out_name.tar.gz

# 把某个特定路径的虚拟环境打包为 my_env.tar.gz
conda pack -p /explicit/path/to/my_env
```

### 解压使用
#### linux
```
# 创建目录 `my_env`，并将环境解压至该目录
mkdir -p my_env
tar -xzf my_env.tar.gz -C my_env

# 使用python而不激活或修复前缀。
# 大多数 python 库可以正常工作，但需要处理前缀的部分将失败。
./my_env/bin/python

# 激活环境，同时这步操作会将路径 `my_env/bin` 添加到环境变量 path
source my_env/bin/activate

# 在环境中运行python
(my_env) $ python

# 从激活环境中清除前缀。
# 请注意，也可以在不激活环境的情况下运行此命令
# 只要机器上已经安装了某个版本的python。
(my_env) $ conda-unpack

# 此时，环境与您在此路径直接使用 conda 安装的环境完全相同。
# 所有脚本都应该工作正常。
(my_env) $ ipython --version

# 停用环境以将其从环境变量 path 中删除
(my_env) $ source my_env/bin/deactivate
```
#### windows

新建 `my_env` 文件夹，将打包的 my_env.tar.gz 文件解压到该文件夹中。

使用 cmd 打开路径 `my_env` 所在路径
```
# 进入项目路径
cd C:\my_env

# 激活环境 
.\Scripts\activate.bat

# 从激活环境中清除前缀。
.\Scripts\conda-unpack.exe

# 退出环境
.\Scripts\deactivate.bat
```
