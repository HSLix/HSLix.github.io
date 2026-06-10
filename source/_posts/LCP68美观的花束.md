---
title: 【Leetcode Daily】LCP68美观的花束
author: 陆爻齐-LuYaoQi
cover: /img/index/leetcode.png
tags:
  - Algorithm
categories:
  - 力扣日常 | LeetcodeDaily
series:
  - 力扣日常 | LeetcodeDaily
hide: true
abbrlink: e5b0d046
date: 2025-05-28 10:31:29
---

# 题目浅析
- 想查看原题可以点击[题目链接](https://leetcode.cn/problems/1GxJYY/description/)。

- 简单地说，就是给一个数组，和一个整数 K，要求返回所有满足条件的子数组数目。条件是子数组内每个数字的数目不超过 K。

# 思路分享
- 很无聊的越短越合法不定长滑动窗口（{% post_link 713乘积小于K的子数组 %}），甚至不需要按照题目所说来取模就可以 ac。

# 代码解答（强烈建议自行解答后再看）
- 参考题解
```c++
class Solution {
public:
    int beautifulBouquet(vector<int>& flowers, int cnt) {
        int ans = 0;
        int left = 0;
        unordered_map<int, int> rec;
        for (int right = 0; right < flowers.size(); right++) {
            int index = flowers[right];
            rec[index]++;
            while(rec[index] > cnt) {
                rec[flowers[left++]]--;
            }
            ans += (right-left+1)%1000000007;
            ans %= 1000000007;
        }
        return ans;
    }
};
```