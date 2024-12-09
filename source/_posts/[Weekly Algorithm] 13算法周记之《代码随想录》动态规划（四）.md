---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（四）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-12-08 23:22:00
hide: false
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，不过周五和周日两门考试太重了，浅做两题，接下来非考试周就继续至少一天一道罢。

本周两道题是完全背包求排列数的应用题和完全背包求 组合/排列 的最小组合数，要记得组合与排列的区别在于背包空间和物品的遍历先后顺序，先遍历物品就不会出现物品乱序填背包的情况，所以是求组合数；而先遍历背包空间，使得同一个空间能有相同物品按不同顺序摆放，则是求排列数。

# 动态规划
- ## 爬楼梯（进阶版）
- [代码随想录](https://programmercarl.com/0070.%E7%88%AC%E6%A5%BC%E6%A2%AF%E5%AE%8C%E5%85%A8%E8%83%8C%E5%8C%85%E7%89%88%E6%9C%AC.html#%E6%80%9D%E8%B7%AF)
- [57. 爬楼梯（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1067)
- 题意，给 n 个台阶，和一起最多能迈 m 个台阶，问有多少不同方法可爬顶端
- 方法，由于同一步子可迈多次，故为完全背包问题，而且求的是排列数，与[[组合总和IV]]类似
- 陆爻齐的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int n, m;
      while(cin >> n >> m){
          vector<int> dp(n+1, 0); // dp[j]到 j 有 dp[j] 个方法
          
          dp[0] = 1; // 初始化，如此上一阶台阶才能有一个
          
          for (int i = 1; i < n+1; i++) { // 先遍历背包容量，后遍历物品，才能求排列数，否则是组合数
              for (int j = 1; j < m+1; j++) {
                  if (i - j >= 0) {
                      dp[i] += dp[i-j];
                  }
              }
          }
          
          cout << dp[n] << endl;
      }
      return 0;
  }
  ```
- ## 零钱兑换
- [代码随想录](https://programmercarl.com/0322.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [322. 零钱兑换 - 力扣（LeetCode）](https://leetcode.cn/problems/coin-change/description/)
- 题意，和[[零钱兑换II]]一样，给硬币面额和目标金额，但求的是凑齐该金额的最小硬币数。
- 方法，由于最小硬币数用组合和排列都行，故先遍历物品还是背包就无所谓了，重点是由于求的是最小，初始化dp数组时不能都是 0，否则递推公式会被覆盖，其它就照常了
	1. dp[j] ，凑齐金额 j 的最小硬币数
	2. 递推公式，dp[j] = min(dp[j], dp[j-coin[i]]+1),不过如果 dp[j-coin[i]] 为初始值，说明这种情况不存在，那就跳过
	3. 初始化，dp[0]=0, dp[other]=INT_MAX
	4. 遍历顺序，从前到后，这是滚动数组，实现完全背包
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int coinChange(vector<int>& coins, int amount) {
          if (amount == 0) return 0;
          vector<int> dp(amount+1, INT_MAX); // dp[j] 为凑齐总金额 j 的最小硬币数
          dp[0] = 0;
  
          for (int i = 0; i < coins.size(); i++) {
              for (int j = coins[i]; j < amount+1; j++) {
                  if (dp[j-coins[i]] == INT_MAX) continue;
                  dp[j] = min(dp[j], dp[j-coins[i]] + 1);
              }
          }
          /*for (auto i : dp) {
              cout << i << " ";
          }
          cout << endl;*/
  
          return (dp[amount]==INT_MAX) ? -1 : dp[amount];
      }
  };
  ```