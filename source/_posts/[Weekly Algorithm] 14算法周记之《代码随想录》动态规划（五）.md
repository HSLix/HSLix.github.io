---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（五）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 22e15e16
date: 2024-12-15 11:06:00
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，本周主要学会了完全背包的应用，多重背包的理论，~~打劫~~打家劫舍问题，和股票买卖问题的入门版。认识到了所存储的状态并不只有 整数 这一种形式，还能有多个整数并成的 vector 作为状态使用。

不得不说，动态规划十分的灵活，一百道题入门还不是说说而已啊。

# 动态规划
- ## 完全平方数
- [代码随想录](https://programmercarl.com/0279.%E5%AE%8C%E5%85%A8%E5%B9%B3%E6%96%B9%E6%95%B0.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [279. 完全平方数 - 力扣（LeetCode）](https://leetcode.cn/problems/perfect-squares/description/)
- 题意，给一个整数 n，求其被完全平方数组成的最小个数。
- 方法，就是[[零钱兑换II]]的变体，给的零钱变成了自己找小于 n 的完全平方数，背包容量为 n
	1. dp[j] 含义为 在背包容量为 j 的情况下，完全平方数的最小组合
	2. 递推公式，dp[i]=min(dp[i],dp[i-j*j]+1)，也就是要么保持不变，要么就在已有的组合上加一
	3. 初始化，dp[0]=0,这是为了递推公式才这么初始化，也能理解，完全平方数不算 0，不会有组合的
	4. 遍历方向，完全背包，从前向后
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int numSquares(int n) {
          vector<int> dp(n+1, INT_MAX);
          dp[0] = 0;
  
          for (int i = 1; i < n+1; i++) {
              for (int j = 0; j*j < n+1; j++) {
                  if (i-j*j >= 0 && dp[i-j*j]!=INT_MAX) dp[i] = min(dp[i], dp[i-j*j]+1);
              }
          }
          /*
          for (auto i : dp) {
              cout << i << " ";
          }
          cout << endl;
          */
          return dp[n];
      }
  };
  ```
- ## 单词拆分
- [代码随想录](https://programmercarl.com/0139.%E5%8D%95%E8%AF%8D%E6%8B%86%E5%88%86.html#%E6%80%9D%E8%B7%AF)
- [139. 单词拆分 - 力扣（LeetCode）](https://leetcode.cn/problems/word-break/description/)
- 题意，给字符串列表，看字符串能否由字符串列表中的字符串拼接而成（可重复利用，不要求全都用上）
- 方法，将字符串看成背包，列表中的字符串就是行李，对于每一处空间，可看其减去列表中字符串大小的前面空间，是否能由字符串列表组成，满足则该空间也能满足，如此便能递推至尾部。但注意本题要求的是排列数而非组合数，因为字符串如何拼接是没有顺序规定的。
	1. dp[j] 的意思是对于字符串 s 的前 j 个字符，能否用字符串列表组成
	2. 递推公式，if (str_list.find(str) != str_list.end() && dp[j]) dp[i] = true;即当前所截取的字符串在字符串列表中出现，且去掉该字符串的部分能由字符串列表组成，则该下标处的字符串也能由字符串列表组成
	3. 初始化，虽然空字符串无法组成，但递推公式决定 dp[0] = true
	4. 遍历方向，先背包空间后物品，从前向后，求排列数
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      bool wordBreak(string s, vector<string>& wordDict) {
          vector<bool> dp(s.size()+1, false); // dp[j] 代表在下标为 j 处能否由字典组成
          dp[0] = true;
          unordered_set<string> rec(wordDict.begin(), wordDict.end());
  
          for (int i = 1; i < s.size()+1; i++) {
              // cout<< "i:" << i <<endl;
              for (int j = 0; j < i; j++) {
                  // cout<< "j:" << j << endl;
                  string word = s.substr(j, i-j);
                  // cout<< "word:" << word << endl;
                  if (rec.find(word) != rec.end() && dp[j]) {
                      dp[i] = true;
                      // cout<< "True i:" << i << endl;
                  }
              }
          }
  
          return dp[s.size()];
      }
  };
  
  ```
- ## 多重背包理论
- [代码随想录](https://programmercarl.com/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80%E5%A4%9A%E9%87%8D%E8%83%8C%E5%8C%85.html#%E5%A4%9A%E9%87%8D%E8%83%8C%E5%8C%85)
- [56. 携带矿石资源（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1066)
- 题意，有 c 个背包空间，给 n 类矿石，每类矿石的重量、价值和最大携带数量
- 方法，其实是 01 背包的变体，因为把最大携带数量摊平，就是 01 背包，不过其中有一部分物品重量和价值相同，但是直接摊平到 vector 让他动态增长会超时，所以有另一个方法，也就是先遍历物品，再遍历空间，然后遍历空间，向前试探。
	1. dp[j] 是在空间 j 上，能容纳的最大价值
	2. 递推公式，dp[j] = max(dp[j], dp[j - k*weigh[i]]+ k * price[i])，也就是要么就当前的价值，要么就因这个最大携带数量，从小到大，向前试探；
	3. 初始化，全 0， 毕竟是找最大价值嘛
	4. 遍历顺序，从前向后
	5. 举例……

- 参考代码随想录思路的方法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int bag_size, n;
      cin >> bag_size >> n;
      vector<int> weigh;
      vector<int> price;
      vector<int> num;
      
      
      for (int i = 0; i < n; i++) {
          int tmp;
          cin >> tmp;
          weigh.push_back(tmp);
      }
      
      for (int i = 0; i < n; i++) {
          int tmp;
          cin >> tmp;
          price.push_back(tmp);
      }
      
      for (int i = 0; i < n; i++) {
          int tmp;
          cin >> tmp;
          num.push_back(tmp);
      }
      vector<int> dp(bag_size+1, 0);
      for (int j = 0; j < n; j++) {
          for (int i = bag_size; i >= weigh[j]; i--) {
              for (int k = 1; k <= num[j] && i - k * weigh[j] >= 0; k++) {
                  dp[i] = max(dp[i], dp[i - k * weigh[j]] + k*price[j]);
              }
          }
      }
      cout << dp[bag_size] << endl;
      
  
      return 0;
  }
  ```
- ## 打家劫舍
- [代码随想录](https://programmercarl.com/0198.%E6%89%93%E5%AE%B6%E5%8A%AB%E8%88%8D.html#%E6%80%9D%E8%B7%AF)
- [198. 打家劫舍 - 力扣（LeetCode）](https://leetcode.cn/problems/house-robber/submissions/586612097/)
- 题意，给一个数组（一组家），求取其中数值相加的最大值，但取值时不能取连续两值（不能连续偷两家）。
- 方法，由于每次取值与上一次有关，故递推公式为 dp[i] = max(dp[i-1], dp[i-2]+nums[i]);，即要么间隔上一个数并加上当前值，要么考虑上一个数。
	1. dp[i] 的意思是在偷到 i 家时，能得到的最大金额；
	2. 递推公式， dp[i] = max(dp[i-1], dp[i-2]+nums[i])
	3. 初始化，全 0，dp[0] = nums[0]; dp[1] = max(nums[0], nums[1]);
	4. 遍历方向，从前向后，其实后向前只要初始化变一变也行（dp[0] = nums[nums.size()-1], dp[1] = max(dp[0], nums[nums.size()-2]);
	5. 举例,……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int rob(vector<int>& nums) {
          if (nums.size() == 0) return 0;
          vector<int> dp(nums.size(), 0); // dp[i] 在偷到 i 家时，窃取金额最高的数字
          if (nums.size() == 1) return nums[0];
          dp[0] = nums[0];
          
          dp[1] = max(nums[0], nums[1]);
          for (int i = 2; i < nums.size(); i++) {
              dp[i] = max(dp[i-1], dp[i-2]+nums[i]);
          }
  
          return dp[nums.size()-1];
      }
  };
  
  ```
- ## 打家劫舍II
- [代码随想录](https://programmercarl.com/0213.%E6%89%93%E5%AE%B6%E5%8A%AB%E8%88%8DII.html#%E6%80%9D%E8%B7%AF)
- [213. 打家劫舍 II - 力扣（LeetCode）](https://leetcode.cn/problems/house-robber-ii/description/)
- 题意，在[[打家劫舍]]基础上，由于房屋成环，也就是首尾算相邻屋子，故不能同时取首尾。
- 方法，考虑有三种情况，1. 只考虑中间的房屋（无首尾）；2. 不考虑尾部屋子；3. 不考虑首部屋子。
  发现情况 1 在 2 和 3 中，故只要考虑 2 和 3 即可。故可对 2 和 3 分别打家劫舍一次，取价值最高的情况。  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int rob(vector<int>& nums) {
         if (nums.size() == 0) return 0;
         if (nums.size() == 1) return nums[0];
         if (nums.size() == 2) return max(nums[0], nums[1]);
  
         int result1 = rogRange(nums, 0, nums.size()-2); // 只考虑首元素
         int result2 = rogRange(nums, 1, nums.size()-1); // 只考虑尾元素
  
         return max(result1, result2);
      }
  
      int rogRange(vector<int> &nums, int start, int end) {
          vector<int> dp(end - start + 1, 0); // dp[j]为偷到 j 家可得最大价值
          //cout << "start:" << start << " " << "end:" << end << endl;
          dp[0] = nums[start];
          dp[1] = max(nums[start], nums[start+1]);
    
          for (int i = start+2; i < end+1; i++) {
              dp[i-start] = max(dp[i-start-1], dp[i-start-2]+nums[i]);
          }
          
          return dp[end-start];
      }
  };
  
  ```
- ## 打家劫舍III
- [代码随想录](https://programmercarl.com/0337.%E6%89%93%E5%AE%B6%E5%8A%AB%E8%88%8DIII.html#%E6%80%9D%E8%B7%AF)
- [337. 打家劫舍 III - 力扣（LeetCode）](https://leetcode.cn/problems/house-robber-iii/description/)
- 题意，本题可打劫的家变成二叉树形式排列，且同时不能偷一个节点和与之直接相连的节点，即其父节点与两个子节点；
- 方法，从递归的角度，采用后序遍历，从下向上，一个个计算并比较自己+孙子与儿子的组合哪个更好，采用 unordered map 来存储一个节点对应的值（该值为计算后可得最大价值）。
  从动态规划的角度，可以让一个节点返回一对数，即不偷该点所得最大价值与偷该点所得最大价值，前者用子节点的最大价值和即可，后者则为该点当前价值加上子节点不偷的价值和。  
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
   *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
   *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
   * };
   */
  class Solution {
  public:
      vector<int> treeRob(TreeNode *root) {
          if (root == nullptr) return {0, 0}; // 不偷当前节点，得 0，偷当前节点，也是0
          //if (root->left == nullptr && root->right == nullptr) return {0, root->val};
          vector<int> left = treeRob(root->left);
          vector<int> right = treeRob(root->right);
  
          int val1 = max(left[0],left[1]) + max(right[0],right[1]);
          int val2 = root->val + left[0] + right[0];
  
          return {val1, val2};
      }
  
      int rob(TreeNode* root) {
          vector<int> tmp = treeRob(root);
          return max(tmp[0], tmp[1]);
      }
  };
  
  ```
- ## 买卖股票的最佳时机
- [代码随想录](https://programmercarl.com/0121.%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [121. 买卖股票的最佳时机 - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)
- 题意，给一个整型数组，分别是多天的股票价格，可买一天。
- 方法，用动态规划，记录一天买与不买持有资金，每天迭代选最大
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int maxProfit(vector<int>& prices) {
          // dp[i][0] 第i天持有金额（买入股票的利润，负数）
          // dp[i][1] 第i天卖出股票的金额
          /*
          vector<vector<int>> dp(prices.size(), vector<int>(2, INT_MIN)); 
          dp[0][0] = -1 * prices[0];
          dp[0][1] = 0;
          for (int i = 1; i < prices.size(); i++) {
              dp[i][0] = max(dp[i-1][0], -1 * prices[i]);
              dp[i][1] = max(dp[i-1][1], prices[i] + dp[i][0]);
          }
          return dp[prices.size()-1][1];
          */
          // dp[i]只依赖于dp[i-1]的状态，优化为滚动数组
          vector<int> dp(2, INT_MIN); 
          dp[0] = -1 * prices[0];
          dp[1] = 0;
          for (int i = 1; i < prices.size(); i++) {
              dp[0] = max(dp[0], -1 * prices[i]);
              dp[1] = max(dp[1], prices[i] + dp[0]);
          }
          return dp[1];
      }
  };
  
  ```
