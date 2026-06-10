---
title: 【Leetcode Daily】208实现Trie（前缀树）
author: 陆爻齐-LuYaoQi
cover: /img/index/leetcode.png
tags:
  - Algorithm
  - 字典树
categories:
  - 力扣日常 | LeetcodeDaily
series:
  - 力扣日常 | LeetcodeDaily
hide: true
abbrlink: df9ac204
date: 2025-12-22 15:58:20
---

# 题目浅析
- 想查看原题可以点击[题目链接](https://leetcode.cn/problems/implement-trie-prefix-tree/description/)。

- 简单地说，就是给一个字符串数组，并且逐个做 insert 或者 search 操作，还有判断搜索是否为前缀。

# 思路分享
- 题目的需求就是经典的字典树可以做到的事，现如今的关键词提示也有用到其中原理。字典树可以看作多叉树，不过每个节点可能会存些信息辅助查询搜索，有的节点可能是代表一个单词的末尾，也可能代表单词的中间。
> https://leetcode.cn/problems/implement-trie-prefix-tree/solutions/2993894/cong-er-cha-shu-dao-er-shi-liu-cha-shu-p-xsj4/

# 代码解答（强烈建议自行解答后再看）
- 参考题解
```python
class Node:
    __slots__ = "son", "end"
    def __init__(self):
        self.son = {}
        self.end = False

class Trie:

    def __init__(self):
        self.root = Node()

    def insert(self, word: str) -> None:
        cur = self.root
        for c in word:
            if c not in cur.son:
                cur.son[c] = Node()
            cur = cur.son[c]
        cur.end = True

    def find(self, word: str) -> int:
        cur = self.root
        for c in word:
            if c not in cur.son:
                return 0
            cur = cur.son[c]
        return 2 if cur.end else 1

    def search(self, word: str) -> bool:
        return self.find(word) == 2

    def startsWith(self, prefix: str) -> bool:
        return self.find(prefix) != 0


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```