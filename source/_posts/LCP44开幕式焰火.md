---
title: 【Leetcode Daily】LCP44开幕式焰火
author: 陆爻齐-LuYaoQi
cover: /img/index/leetcode.png
tags:
  - Algorithm
  - 二叉树
categories:
  - 力扣日常 | LeetcodeDaily
series:
  - 力扣日常 | LeetcodeDaily
hide: true
abbrlink: a1ce21d4
date: 2026-01-20 10:24:45
---

# 题目浅析
- 想查看原题可以点击[题目链接](https://leetcode.cn/problems/sZ59z6/description/)。

- 简单地说，就是用一个二叉树代表焰火，节点代表部分焰火颜色种类，求焰火中有多少种颜色。

# 思路分享
- 难得与灵神几乎相同。总之就是 dfs 遍历，外面用哈希表（集合 set）记录颜色种类，最终看哈希表的大小（长度）就行，利用了哈希表来去重。
> https://leetcode.cn/problems/sZ59z6/solutions/1016949/go-dfsha-xi-biao-by-endlesscheng-lqli/

# 代码解答（强烈建议自行解答后再看）
- 参考题解
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def numColor(self, root: TreeNode) -> int:
        cnt = set()
        def dfs(root: TreeNode):
            if not root:
                return
            cnt.add(root.val)
            dfs(root.left)
            dfs(root.right)
        dfs(root)
        return len(cnt)
```