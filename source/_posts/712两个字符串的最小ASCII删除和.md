---
title: 【Leetcode Daily】712两个字符串的最小ASCII删除和
author: 陆爻齐-LuYaoQi
cover: /img/index/leetcode.png
tags:
  - Algorithm
  - 动态规划
categories:
  - 力扣日常 | LeetcodeDaily
series:
  - 力扣日常 | LeetcodeDaily
hide: true
abbrlink: e1bbcc36
date: 2025-11-21 11:02:28
---

# 题目浅析
- 想查看原题可以点击[题目链接](https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/)。

- 简单地说，就是给两个字符串，要将这些字符串各自删除一些字符，使得两边一样，但是删去一个字符的代价是其对应的 ASCII 码值，求最小的这个值。

# 思路分享
- 选或不选的思路，如果两边的字符相等，那么都选就不用代价；但是如果字符不相等，就要考虑一边不选（包括了两边都不选的情况），哪边不选就要加上对应字符的 ASCII 值。
> https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/solutions/2835393/ling-shen-dpti-dan-da-qia-ji-yi-hua-sou-cmf9z/

# 代码解答（强烈建议自行解答后再看）
- 参考题解
```python
class Solution:
    def minimumDeleteSum(self, s1: str, s2: str) -> int:
        # 空间优化
        m, n = len(s1), len(s2)
        f = [0]*(n+1)
        
        for i, x in enumerate(s2):
            f[i+1] = f[i] + ord(x)

        s = 0
        for i in range(m):
            pre = s
            s += ord(s1[i])
            f[0] = s
            for j in range(n):
                tmp = f[j+1]
                if s1[i] == s2[j]:             
                    f[j+1] = pre
                else:
                    f[j+1] = min(f[j+1]+ord(s1[i]), f[j]+ord(s2[j]))
                pre = tmp
        return f[n]
        # 递推
        m, n = len(s1), len(s2)
        f = [[0]*(n+1) for _ in range(m+1)]
        for i, x in enumerate(s1):
            f[i+1][0] = f[i][0] + ord(x)
        for i, x in enumerate(s2):
            f[0][i+1] = f[0][i] + ord(x)
        for i in range(m):
            for j in range(n):
                if s1[i] == s2[j]:
                    f[i+1][j+1] = f[i][j]
                else:
                    f[i+1][j+1] = min(f[i][j+1]+ord(s1[i]), f[i+1][j]+ord(s2[j]))
        return f[m][n]
        # 记忆化搜索
        @cache
        def dfs(i:int, j:int):
            if i < 0 or j < 0:
                if i < 0 and j < 0:
                    return 0
                if i < 0:
                    return sum(ord(x) for x in s2[0:j+1])
                if j < 0:
                    return sum(ord(x) for x in s1[0:i+1])
            if s1[i] == s2[j]:
                return dfs(i-1, j-1)
            return min(dfs(i-1, j) + ord(s1[i]), dfs(i, j-1) + ord(s2[j]))
        return dfs(len(s1)-1, len(s2)-1)
```