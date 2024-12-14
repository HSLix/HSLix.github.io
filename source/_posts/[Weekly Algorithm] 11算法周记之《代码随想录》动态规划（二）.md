---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（二）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: b74c2166
date: 2024-11-24 12:41:00
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，由于期末月的到来，以及题目难度的增加，暂且将动态规划的学习进度放慢一点。下面继续巩固五步法。
1. 分析 dp 数组及其下标含义
2. 确定递推公式
3. 确定 dp 数组初始化
4. 确定遍历顺序
5. 举例分析 dp 数组是否如所想迭代

只能感叹，这动态规划真是一百道题才入门啊。

# 动态规划
- ## 分割等和子集
- [代码随想录](https://programmercarl.com/0416.%E5%88%86%E5%89%B2%E7%AD%89%E5%92%8C%E5%AD%90%E9%9B%86.html#%E6%80%9D%E8%B7%AF)
- [416. 分割等和子集 - 力扣（LeetCode）](https://leetcode.cn/problems/partition-equal-subset-sum/)
- 题意，给一个数组，返回判断该数组是否能平分为和相等的两部分
- 方法，直觉上，可以用回溯遍历树，找到那个子节点，但复杂度太高。
  本题其实就是 01 背包的应用，nums[i] 本身，是物品，重量，以及价值。数组能否平分和的本质，就是平分和的大小的背包能否被刚好装满，也就是下标与值相等。  
- 参考代码随想录思路的代码
  ```
  class Solution {
  public:
      bool canPartition(vector<int>& nums) {
          int sum = accumulate(nums.begin(), nums.end(), 0); // 求整个数组之和
          if (sum % 2 == 1) return false; // 不可能平分
          int target = sum / 2;
          vector<int> dp(10001, 0); // 由于总和不超 20000，最多 10001 元素足矣，dp[j]是在 j 内最接近 j 的和
          for (int i = 0; i < nums.size(); i++) {
              for (int j = target; j >= nums[i]; j--) {
                  dp[j] = max(dp[j], dp[j-nums[i]]+nums[i]);
              }
          }
  
          return dp[target]==target; // 也就是在 target 处刚好有和能与之相等
      }
  };
  
  ```
- ## 最后一块石头的重量II
- [代码随想录](https://programmercarl.com/1049.%E6%9C%80%E5%90%8E%E4%B8%80%E5%9D%97%E7%9F%B3%E5%A4%B4%E7%9A%84%E9%87%8D%E9%87%8FII.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [1049. 最后一块石头的重量 II - 力扣（LeetCode）](https://leetcode.cn/problems/last-stone-weight-ii/)
- 题意，从整型数组中，不断选取两数，使得大数减去小数同时小数消失，直到最后只剩一数，求剩下数字的最小值
- 方法，本质上，其实就是[[分割等和子集]]，还挺难看出来的。下面是五步法。
	1. 确定 dp 数组及其下标含义，dp[j] 的意思是不大于 j 的重量内，最接近 j 的数值；
	2. 确认递推式，由于石头的重量和价值一体，故 dp[j]=max(dp[j], dp[j-stone[i]]+stone[i]);
	3. 初始化，要避免初始值覆盖结果，而且重量不为负数，故初始化为 0，即可；
	4. 遍历顺序，从前往后即可，没有一定的要求
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int lastStoneWeightII(vector<int>& stones) {
          int sum = accumulate(stones.begin(), stones.end(), 0);
          int target = sum / 2;
          vector<int> dp(target+1, 0); // 由于重量不为负数，初始化0比较合适
  
          for (int i = 0; i < stones.size(); i++) {
              for (int j = target; j >= stones[i]; j--) {
                  dp[j] = max(dp[j], dp[j-stones[i]]+stones[i]);// 石头的重量和价值同义
              }
          }
  
          return (sum - dp[target]) - dp[target];// 前者为剩下石头，后者为分出来不大于一半的石头
      }
  };
  ```
- ## 目标和
- [代码随想录](https://programmercarl.com/0494.%E7%9B%AE%E6%A0%87%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
- [494. 目标和 - 力扣（LeetCode）](https://leetcode.cn/problems/target-sum/description/)
- 题意，给一个数组和目标整数，可以数组每个数前用加号或减号，看最后有几个组合可得到目标数
- 方法，回溯有类似题目，但普通回溯会超时。这里首先要将其转化为 01 背包问题，数组很显然就是行李，需要做一些变换，从两个方程式可知为达成目标值，左边为正号的数字之和，故以此为背包容量，本质就是求有多少个数之和为此和。
	1. 确定 dp 数组及其下标含义，dp[i][j] 是考虑 0-i 的数字时，目标和为 j 的方案数；
	2. 确定递推公式，一个方案多考虑一个物品时相当于，原方案数+必须带上该物品且剩下空间的方案数，dp[i][j] = dp[i-1][j]+dp[i-1][j-nums[i]]
	3. 初始化，第一行就只有物品大小的那个下标赋值为1，第一列则得看中间 0 的个数，每遇到一个 0，后面放空间 0 的起始方案数就会 * 2
	4. 递推顺序，从前向后（滚动数组的空间从后向前）
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int findTargetSumWays(vector<int>& nums, int target) {
          int sum = accumulate(nums.begin(), nums.end(), 0); // 总和
          if (target > sum) return 0;
          if ((sum+target) % 2 == 1 || (sum+target) < 0) return 0;
  
          /*
          left + right = sum
          left - right = target
          两式相加除 2 得 left
          */
          int left = (sum + target) / 2;// 也就是为达成目标和，左边为正号的和
          vector<vector<int>> dp(nums.size(), vector<int>(left+1, 0));
          // dp[i][j] 是从0-i下标的数字中，凑出和为 j 的方法
          // 初始化第一列，容量为0，只有不放一种方法，但是如果是 0，那么该位正负皆可
          int numZero = 0;
          for (int i = 0; i < nums.size(); i++) {
              if (nums[i] == 0) numZero++;
              dp[i][0] = (int) pow(2.0, numZero);
          }
          
          // 初始化第一行，只有数值恰好相等为1
          for (int i = 1; i < left+1; i++) {
              if (nums[0] > i) continue;
              else if (nums[0] == i) dp[0][i] = 1;
              else break;
          }
  
          for (int i = 1; i < nums.size(); i++) {
              for (int j = 0; j < left+1; j++) {
                  if (j < nums[i]) dp[i][j] = dp[i-1][j];
                  else dp[i][j] = dp[i-1][j] + dp[i-1][j-nums[i]]; // 相当于不用该物品的旧方法和用该物品的新方法之和
              }
          }
  
          for (int i = 0; i < nums.size(); i++) {
              for (int j = 0; j < left+1;j++) {
                  cout << dp[i][j] << " ";
              }
              cout << endl;
          }
          cout << endl;
  
          return dp[nums.size()-1][left];
      }
  };
  
  ```
- ## 一和零
- [代码随想录](https://programmercarl.com/0474.%E4%B8%80%E5%92%8C%E9%9B%B6.html#%E6%80%9D%E8%B7%AF)
- [474. 一和零 - 力扣（LeetCode）](https://leetcode.cn/problems/ones-and-zeroes/submissions/582275874/)
- 题意，给一个各字符串只由 0 和 1 构成的字符串数组，再给两个整数，要求给出在 0 和 1 分别不超过这两数的情况下，最大子集的字符串个数（真子集）
- 方法，该题也是 01 背包，这两个分别代表最大 0 和 1 的数相当于两个背包容量的维度，实质上就是要求在这两个维度的束缚下，能装的最大的字符串个数
	1. dp[i][j]，最多有 i 个 0 和 j 个 1 的情况下，能装入的最多字符串个数
	2. 递推公式，dp[i][j]=max(dp[i][j], dp[i-zero_num][j-one_num];意思是在原来的情况，与必须装入该字符串的情况相比，选取可装字符串最多的情况
	3. 初始化，由于是求个数，全数组初始化为 0 即可
	4. 遍历顺序，由于是二维滚动数组，从后向前
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int findMaxForm(vector<string>& strs, int m, int n) {
          int zero_num, one_num; // 记录每个字符串的 0 和 1 数
          vector<vector<int>> dp(m+1, vector<int>(n+1, 0)); // dp[i][j] 指在 i 和 j 的限制数内，包含最大字符串个数
          for (string str : strs) {
              zero_num = 0; one_num = 0;
              for (char c : str) {
                  if (c == '0') {
                      zero_num++;
                  }
                  else {
                      one_num++;
                  }
              }
  
              // 实质就是比较原来的情况与必须加上这个字符串的情况，哪个装的字符串多
              for (int i = m; i >= zero_num; i--) {
                  for (int j = n; j >= one_num; j--) {
                      dp[i][j] = max(dp[i][j], dp[i-zero_num][j-one_num]+1);
                  }
              }
          }
          return dp[m][n];
      }
  };
  
  ```