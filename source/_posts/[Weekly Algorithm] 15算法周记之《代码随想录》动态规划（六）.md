---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（六）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 47ef15cf
date: 2024-01-12 10:06:00
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，本周主要学习完买卖股票相关内容，并初步探索子序列的部分。

买卖股票的题有明显的状态转换，但子序列就没那么明显。

# 动态规划
- ## 买卖股票的最佳时机II
- [代码随想录](https://programmercarl.com/0122.%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BAII%EF%BC%88%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%EF%BC%89.html#%E6%80%9D%E8%B7%AF)
- [122. 买卖股票的最佳时机 II - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/description/)
- 题意，在[[买卖股票的最佳时机]]的基础上，要求可以多次购买股票，但同一时段只能持有一支股票，也就是再卖出上一支股票前，不允许购入下一支股票。
- 方法，与[[买卖股票的最佳时机]]的差别，在于考虑购入当前股票时，是在上一支股票考虑购入和卖出的两个情况中取最大，并非直接取历史最便宜股票和当前股票对比。
- 参考代码随想录思路解法
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices) {
          // dp[i][0] 考虑第i支股票时，考虑买入的情况；dp[i][1] 考虑第i支股票时，卖出的情况
          int len = prices.size();
          vector<vector<int>> dp(len, vector<int>(2, 0));
          dp[0][0] -= prices[0];
          dp[0][1] = 0;
  
          for (int i = 1; i < len; i++) {
              dp[i][0] = max(dp[i-1][0], dp[i-1][1]-prices[i]);
              dp[i][1] = max(dp[i-1][1], dp[i-1][0]+prices[i]);
          }
  
          return dp[len-1][1];
      }
  };
  
  ```
- ## 买卖股票的最佳时机III
- [代码随想录](https://programmercarl.com/0123.%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BAIII.html#%E6%80%9D%E8%B7%AF)
- [123. 买卖股票的最佳时机 III - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)
- 题意，在[[买卖股票的最佳时机II]]基础上，要求最多只能进行两次股票的购入和卖出。
- 方法，可以想想此前两题的 dp，dp[i] 代表第 i 天，后面是状态，有两状态就是持有和卖出。换到此题就是状态更多了，故本题可设 5 个状态，0 代表不操作（可省略），1 代表第一次持有（不代表一定购入，可能是之前的购入），2代表第一次卖出，3 是第二次持有，4 是第二次卖出。后面的状态均依赖于自己与前一个状态。
	1. dp[i][0-4]，意思如上
	2. 递推公式，对于持有的状态，dp[i][1] = max(dp[i-1][0]-price[i],dp[i-1][1])，就是在不购买的基础上购买当日股票和采用之前购买股票的抉择；对于卖出状态，dp[i][2] = max(dp[i-1][1]+price[i], dp[i-1][2]); 就是在前一日购买股票的基础上，售出当日股票与延续此前卖出的抉择；
	3. 初始化，dp[0][0] = 0, 不作为当然为 0，dp[0][1] = -1 * price[0]; 购入股票成本, dp[0][2] = 0，马上卖出即无收益；dp[0][3] = -1 * price[0]，当日连续第二次买入; dp[0][4] = 0; 当日连续第二次卖出。
	4. 遍历方向，从前向后
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices) {
          // 分五个状态，0 是无操作，1 是第一次持有，2 是第一次卖出，3是第二次持有，4是第二次卖出
          vector<vector<int>> dp(prices.size(), vector<int>(5, 0));
          dp[0][1] -= prices[0];
          dp[0][3] -= prices[0];
  
          for (int i = 1; i < prices.size(); i++) {
              dp[i][1] = max(dp[i-1][0]-prices[i], dp[i-1][1]);
              dp[i][2] = max(dp[i-1][1]+prices[i], dp[i-1][2]);
              dp[i][3] = max(dp[i-1][2]-prices[i], dp[i-1][3]);
              dp[i][4] = max(dp[i-1][3]+prices[i], dp[i-1][4]);
          }
  
          return dp[prices.size()-1][4];
      }
  };
  
  ```
- ## 买卖股票的最佳时机IV
- [代码随想录](https://programmercarl.com/0188.%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BAIV.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [188. 买卖股票的最佳时机 IV - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)
- 题意，在[[买卖股票的最佳时机III]]基础上，将购买股票的次数由 2 次，拓展为变量 k 次
- 方法，将dp数组拓展为 price.size() 个 2*k+1 的 vector，总共有 2*k+1 个状态，其余的思想与方法，同[[买卖股票的最佳时机III]]。
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int maxProfit(int k, vector<int>& prices) {
          // dp[i][0] 为不操作，dp[i][2*k-1] 为第k笔交易持有, dp[i][2*k] 为第k笔交易卖出，k>=1
          vector<vector<int>> dp(prices.size(), vector<int>(2*k+1, 0));
  
          // 初始化，对于奇数只是单纯买第一天的负价格，而偶数则是一买就卖的 0
          for (int i = 1; i < 2 * k; i+=2) {
              dp[0][i] -= prices[0];
          }
  
          // 对往后每一行奇偶，分别是前一笔交易与上一天同笔交易的比较
          for (int i = 1; i < prices.size(); i++) {
              for (int j = 1; j < 2*k+1; j+=2) {
                  dp[i][j] = max(dp[i-1][j], dp[i-1][j-1]-prices[i]);
                  dp[i][j+1] = max(dp[i-1][j+1], dp[i-1][j]+prices[i]);
              }
          }
  
          return dp[prices.size()-1][2*k];
      }
  };
  
  ```
- ## 买卖股票的最佳时机含冷冻期
- [代码随想录](https://www.programmercarl.com/0309.%E6%9C%80%E4%BD%B3%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E6%97%B6%E6%9C%BA%E5%90%AB%E5%86%B7%E5%86%BB%E6%9C%9F.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [309. 买卖股票的最佳时机含冷冻期 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)
- 题意，在 [[买卖股票的最佳时机II]] 可多次买卖股票的基础上，加入冷冻期，即在卖出股票的一天后，才能再买入股票。
- 方法，引入四种状态，买入，今日卖出，保持卖出，冷冻期。
  买入状态可能有前一日的买入状态、保持卖出、冷冻期推出；  
  今日卖出只会由前一日买入状态卖出今日价格得出；  
  冷冻期保存前一日的今日卖出状态；  
  保持卖出则由前一日的保持卖出、冷冻期推出；  
  
  dp[i][j] 则是 i 天的第 j 个状态  
  递推公式由上述的文字推出  
  初始化则是第一日的买入状态为价格负数，其余为零  
  遍历方向从前到后  
  举例……  
- 参考代码随想录思想的解法
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices) {
          int n = prices.size();
          if (n==0) return 0;
          vector<vector<int>>dp(n, vector<int>(4, 0));
          // dp 有四种状态 1 买入；2 今日卖出；3 保持卖出； 4 冷静期
          // 3 可能为 2 或 4 的延续
          dp[0][0] -= prices[0];
          for (int i = 1; i < n; i++) {
              dp[i][0] = max(dp[i-1][0], max(dp[i-1][2] - prices[i], dp[i-1][3] - prices[i]));
              dp[i][1] = dp[i-1][0] + prices[i];
              dp[i][2] = max(dp[i-1][2], dp[i-1][3]);
              dp[i][3] = dp[i-1][1];
          }
  
          return max(dp[n-1][1], max(dp[n-1][2], dp[n-1][3]));
      }
  };
  
  ```
- ## 买卖股票的最佳时机含手续费
- [代码随想录](https://www.programmercarl.com/0714.%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA%E5%90%AB%E6%89%8B%E7%BB%AD%E8%B4%B9%EF%BC%88%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%EF%BC%89.html)
- [714. 买卖股票的最佳时机含手续费 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)
- 题意，在[[买卖股票的最佳时机II]]可多次交易的基础上，添加手续费，即每次交易后需扣除一定手续费。
- 方法，与[[买卖股票的最佳时机II]]的唯一差别在于递推公式，将卖出的部分添加手续费即可。
  
  不过，陆爻齐由于太久没碰相关部分，想自己从零推出来，于是整合了三个状态，写下下面的代码  
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices, int fee) {
          // 我觉着有三种状态
          // 第一是直接买入，第二是今日卖出
          // 与之前只会多次交易的题相比，多了个手续费，减一下就行了
          vector<vector<int>> dp(prices.size(), vector<int>(3, 0));
  
          // 递推公式的话，买入就直接取当日价格负数减下去，卖出则取前一日买入加上这一日卖出
          // 初始化，除买入，取0
          dp[0][0] -= prices[0];
          for (int i = 1; i < prices.size(); i++) {
              dp[i][0] = max(dp[i-1][0], dp[i-1][2]-prices[i]);
              dp[i][1] = dp[i-1][0] + prices[i] - fee;
              dp[i][2] = max(dp[i-1][2], dp[i][1]);
          }
  
          // for (int i = 0; i < prices.size(); i++) {
          //     cout << dp[i][0] << " " << dp[i][1] << " " << dp[i][2] << endl;
          // }
  
          return max(dp[prices.size()-1][1], dp[prices.size()-1][2]);
      }
  };
  
  ```
  看完发现，第三个状态确实可以与第二个状态合并，要追求简洁的情况下，每个状态应是由过去的部分推导出来的，如果有状态与现有状态有关，则可考虑适当合并  
  
  于是可以优化成下面的代码，与代码随想录思路相近了  
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices, int fee) {
          // 我觉着有三种状态
          // 第一是直接买入，第二是今日卖出
          // 与之前只会多次交易的题相比，多了个手续费，减一下就行了
          vector<vector<int>> dp(prices.size(), vector<int>(2, 0));
  
          // 递推公式的话，买入就直接取当日价格负数减下去，卖出则取前一日买入加上这一日卖出
          // 初始化，除买入，取0
          dp[0][0] -= prices[0];
          for (int i = 1; i < prices.size(); i++) {
              dp[i][0] = max(dp[i-1][0], dp[i-1][1]-prices[i]);
              dp[i][1] = max(dp[i-1][1], dp[i-1][0] + prices[i] - fee);
          }
  
          // for (int i = 0; i < prices.size(); i++) {
          //     cout << dp[i][0] << " " << dp[i][1] << " " << dp[i][2] << endl;
          // }
  
          return dp[prices.size()-1][1];
      }
  };
  
  ```
- ## 最长递增子序列
- [代码随想录](https://www.programmercarl.com/0300.%E6%9C%80%E9%95%BF%E4%B8%8A%E5%8D%87%E5%AD%90%E5%BA%8F%E5%88%97.html)
- [300. 最长递增子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-increasing-subsequence/description/)
- 题意：给一个数组，求其中最长递增子序列的长度，该子序列可由删除数组中元素得出。
- 方法：用 dp[i] 表示下标 i 之前的最长递增子序列长度，通过循环得出。
  递推公式：dp[i] = max(dp[i], dp[j]+1); j遍历 0 到 i 的元素，该元素数值小于 i 的元素  
  初始化，均初始化为 1，长度至少为 1；  
  遍历顺序：从前往后，毕竟是递增  
  举例：……  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int lengthOfLIS(vector<int>& nums) {
          vector<int> dp(nums.size(), 1);
          // dp[i]为地址为 i 的最长子序列长度
          int max_len = 1;
          // 外循环遍历数组
          for (int i = 1; i < nums.size(); i++) {
              // 内循环取到已有的最长子序列长度
              for (int j = 0; j <= i; j++) {
                  if (nums[i] > nums[j]) {
                      dp[i] = max(dp[i], dp[j]+1);
                  }
              }
              if (dp[i] > max_len) max_len = dp[i];
          }
  
          return max_len;
      }
  };
  
  ```
- ## 最长连续递增序列
- [代码随想录](https://www.programmercarl.com/0674.%E6%9C%80%E9%95%BF%E8%BF%9E%E7%BB%AD%E9%80%92%E5%A2%9E%E5%BA%8F%E5%88%97.html)
- [674. 最长连续递增序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-continuous-increasing-subsequence/)
- 题意：与[[非递减子序列]]相比，要求子序列得是连续的，中间数据不能删。
- 方法，不需要代码随想录的思路参考，自己都能分析出来。
  dp[i] 是下标 i 为末尾的最长连续递增序列长度  
  递推公式为 dp[i] = dp[i-1]+1; 前提是 dp[i] > dp[i-1]  
  初始化 全初始化为1  
  遍历方向从前向后  
  举例……  
- 解法（与代码随想录思路相近）
  ```
  class Solution {
  public:
      int findLengthOfLCIS(vector<int>& nums) {
          vector<int> dp(nums.size(), 1); // dp[i] 展示前面某一点到下标 i，最长连续递增子序列长度
          int max_len = 1;
  
          for (int i = 1; i < nums.size(); i++) {
              if (nums[i]>nums[i-1]) {
                  dp[i] = dp[i-1]+1;
                  if (dp[i] > max_len) max_len = dp[i];
              }
          }
  
          return max_len;
      }
  };
  
  ```