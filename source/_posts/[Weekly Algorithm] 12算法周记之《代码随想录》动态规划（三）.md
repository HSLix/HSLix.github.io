---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（三）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-12-01 22:40:00
hide: false
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，主要是完全背包的入门，体会其与 01 背包的不同与关联。

下周两门考试，教务脑子真是进了水，搞这么紧干嘛。

# 动态规划
- ## 完全背包理论
- [代码随想录](https://programmercarl.com/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80%E5%AE%8C%E5%85%A8%E8%83%8C%E5%8C%85.html#%E5%85%B6%E4%BB%96%E8%AF%AD%E8%A8%80%E7%89%88%E6%9C%AC)
- [52. 携带研究材料（第七期模拟笔试）](https://kamacoder.com/problempage.php?pid=1052)
- 题目，与 [[01背包理论]]的差别在于一个物品可携带多次，求最大价值
- 方法，
	1. dp[j]，在遍历物品时，截止到空间为 j，可装入的最大价值
	2. 递推公式，dp[j]=max(dp[j],dp[j-weigh[i]]+val[i])
	3. 初始化，全 0
	4. 遍历方向，重点，这是与 01 背包区别最大的地方，01 的滚动数组强调倒序遍历就是为了避免重复，在完全背包中，恰恰需要正序从而利用重复装入物品
	5. 举例，……

- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int n, v;
      cin >> n >> v;
      vector<int> weigh;
      vector<int> val;
      // 初始化输入
      for (int i = 0; i < n; i++) {
          int tmp_w, tmp_v;
          cin >> tmp_w >> tmp_v;
          weigh.push_back(tmp_w);
          val.push_back(tmp_v);
      }
      
      vector<int> dp(v+1, 0);
      
      for (int i = 0; i < n; i++) {
          for (int j = weigh[i]; j <= v; j++) {
              dp[j] = max(dp[j], dp[j-weigh[i]]+val[i]);
          }
      }
      cout << dp[v] << endl;
      return 0;
  }
  ```
- ## 零钱兑换II
- [代码随想录](https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [518. 零钱兑换 II - 力扣（LeetCode）](https://leetcode.cn/problems/coin-change-ii/description/)
- 题意，给硬币面额和目标面额，求凑出目标面额的硬币组合数（ 122 和 221 算重复组合）
- 方法，
	1. dp[j] 是在目标面额为 j 时，组合数
	2. 递推公式，dp[j] += dp[j-coin[i]]；
	3. 初始化，由于递推公式，决定了 dp[0]=1
	4. 遍历方向，这是重点，不可颠倒，求组合数就要先遍历硬币面额，后遍历目标面额；求排列数就要先遍历目标面额，后遍历硬币面额；可以这么理解，先遍历硬币面额，就可以让后面 dp[j] 的硬币大小顺序固定，不会出现 221 这种情况。
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int change(int amount, vector<int>& coins) {
          vector<int> dp(amount+1, 0); // dp[j] 代表考虑前i种硬币，可以凑出多少种组合
          dp[0] = 1;
  
          for (int i = 0; i < coins.size(); i++) {
              for (int j = coins[i]; j < amount+1; j++) {
                  if (dp[j] < INT_MAX - dp[j-coins[i]]) 
                      dp[j] += dp[j-coins[i]];
              }
          }
  
          return dp[amount];
      }
  };
  
  ```
- ## 组合总和IV
- [代码随想录](https://programmercarl.com/0377.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8C%E2%85%A3.html#%E6%80%9D%E8%B7%AF)
- [377. 组合总和 Ⅳ - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-iv/description/)
- 题意，有一个整数数组与一个目标整数，求整数数组中和为目标数的排列数，其值必为 int 范围内
- 方法，与[[零钱兑换II]]相似，不同的是，求排列数需要先遍历背包容量，再遍历物品，才能达到取到乱序的效果
- 陆爻齐试探出的解法，与代码随想录思路相近
  ```
  class Solution {
  public:
      int combinationSum4(vector<int>& nums, int target) {
          // dp[j] 即在目标为 j 时，元素组合个数
          vector<int> dp(target+1, 0);
          dp[0] = 1;
  
          for (int j = 0; j < target+1; j++) {
              for (int i = 0; i < nums.size(); i++) {
                  if (j >= nums[i] && dp[j] < INT_MAX - dp[j-nums[i]]) dp[j] += dp[j-nums[i]];
              }
          }
  
          return dp[target];
      }
  };
  
  ```