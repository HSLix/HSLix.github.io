---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（七）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
date: 2024-01-19 20:12:00
---
# 学习小结
本周跟随《代码随想录》继续推进动态规划的相关学习，本周主要学习子序列相关内容，其中的编辑距离在网络搜索引擎的期末考试见过。但通过本次学习，逐渐感受到在算法，或者说计算机的世界中，很多事情往往会通过抽象来变得简洁从而易于处理。

# 动态规划
- ## 最长重复子数组
- [代码随想录](https://www.programmercarl.com/0718.%E6%9C%80%E9%95%BF%E9%87%8D%E5%A4%8D%E5%AD%90%E6%95%B0%E7%BB%84.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [718. 最长重复子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/description/)
- 题意：给两个数组，问两个数组中最长重复的子数组长度是什么
- 方法：用二维dp，dp[i][j] 代表第一个数组 i-1 为尾部和第二个数组 j-1 处为尾部，最长重复子数组长度。
  递推公式，当 num1[i-1]==num2[j-1]，即可 dp[i][j] = dp[i-1][j-1]+1;  
  初始化，都为0  
  遍历顺序，无所谓，不过从前向后就可  
  举例……  
- 参考代码随想录思路的解法：
  ```
  class Solution {
  public:
      int findLength(vector<int>& nums1, vector<int>& nums2) {
          vector<vector<int>> dp(nums1.size()+1, vector<int>(nums2.size()+1, 0));
          
          int max_len = 0;
  
          for (int i = 1; i < nums1.size()+1; i++) {
              for (int j = 1; j < nums2.size()+1; j++) {
                  if(nums1[i-1] == nums2[j-1]) {
                      dp[i][j] = dp[i-1][j-1] + 1;
                      if (dp[i][j] > max_len) max_len = dp[i][j];
                  }
              }
          }
  
          return max_len;
      }
  };
  
  ```
  优化一下，用滚动数组，注意每次从后向前遍历，以及刷0  
  ```
  class Solution {
  public:
      int findLength(vector<int>& nums1, vector<int>& nums2) {
          // vector<vector<int>> dp(nums1.size()+1, vector<int>(nums2.size()+1, 0));
          vector<int> dp(nums2.size()+1, 0);
          
          int max_len = 0;
  
          for (int i = 1; i < nums1.size()+1; i++) {
              for (int j = nums2.size(); j > 0; j--) {
                  if(nums1[i-1] == nums2[j-1]) {
                      dp[j] = dp[j-1] + 1;
                      if (dp[j] > max_len) max_len = dp[j];
                  }
                  else {
                      dp[j] = 0; // 需要刷零避免前两层的记录影响
                  }
              }
          }
  
          return max_len;
      }
  };
  
  ```
- ## 最长公共子序列
- [代码随想录](https://www.programmercarl.com/1143.%E6%9C%80%E9%95%BF%E5%85%AC%E5%85%B1%E5%AD%90%E5%BA%8F%E5%88%97.html#%E6%80%9D%E8%B7%AF)
- [1143. 最长公共子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-common-subsequence/description/)
- 题意，给两个字符串，给出两串的最长公共子序列的长度
- 方法：与[[最长重复子数组]]相近，不过本题不要求连续，所以递推公式除了上一个相同+1之外，还保存两个子序列可能的最长情况。
- 参考代码随想录的解法
  ```
  class Solution {
  public:
      int longestCommonSubsequence(string text1, string text2) {
          vector<vector<int>> dp(text1.size()+1, vector<int>(text2.size()+1, 0));
  
          for (int i = 1; i < text1.size()+1; i++) {
              for (int j = 1; j < text2.size()+1; j++) {
                  if (text1[i-1] == text2[j-1]) {
                      dp[i][j] = dp[i-1][j-1]+1;
                  }
                  else {
                      dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                  }
              }
          }
  
          // for (int i = 0; i < text1.size()+1; i++) {
          //     for (int j = 0; j < text2.size()+1; j++) {
          //         cout << dp[i][j] << " ";
          //     }
          //     cout << endl;
          // }
  
          return dp[text1.size()][text2.size()];
      }
  };
  
  ```
- ## 不相交的线
- [代码随想录](https://www.programmercarl.com/1035.%E4%B8%8D%E7%9B%B8%E4%BA%A4%E7%9A%84%E7%BA%BF.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [1035. 不相交的线 - 力扣（LeetCode）](https://leetcode.cn/problems/uncrossed-lines/)
- 题意：两个整型数组，相同数字可以两两相连，但是线不能交错。求最大连线数。
- 方法：换了个题意的[[最长公共子序列]]，除了题目，其余都一样，其余省略。
- ## 最大子序和
- [代码随想录](https://www.programmercarl.com/0053.%E6%9C%80%E5%A4%A7%E5%AD%90%E5%BA%8F%E5%92%8C%EF%BC%88%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%EF%BC%89.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [53. 最大子数组和 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-subarray/description/)
- 题意：给一个数组，找个其中子序列之和最大的和。
- 方法：
  dp[i] 是到下标为 i 为尾部的子序列最大和数值  
  递推公式比较有意思，两种情况，一个是延续之前的最大和 dp[i] = dp[i-1]+num[i];或者不延续，从 i 处新开 dp[i] = num[i]，两种情况取较大数值即可。  
  初始化，dp[0] 取 num[0],其它的初始化不重要，毕竟都会被覆盖。  
  遍历顺序：从前向后。  
  举例：……  
- 参考代码随想录思路的方法
  ```
  class Solution {
  public:
      int maxSubArray(vector<int>& nums) {
          vector<int> dp(nums.size(), 0);
          dp[0] = nums[0];
          int result = dp[0];
          for (int i = 1; i < nums.size(); i++) {
              dp[i] = max(dp[i-1]+nums[i], nums[i]);
              if (dp[i] > result) {
                  result = dp[i];
              }
          }
  
          // for (int i = 0; i < nums.size(); i++) {
          //     cout << dp[i] << " ";
          // }
          // cout << endl;
  
          return result;
      }
  };
  
  ```
- ## 判断子序列
- [代码随想录](https://www.programmercarl.com/0392.%E5%88%A4%E6%96%AD%E5%AD%90%E5%BA%8F%E5%88%97.html#%E6%80%9D%E8%B7%AF)
- [392. 判断子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/is-subsequence/submissions/595432953/)
- 题意：给两个字符串序列，判断一个字符串是否为另一个字符串的子序。
- 方法：其实[[最长公共子序列]]类似，只是说判断公共字符串的长度是否为子串长度相等，而且递推公式中，如果两个字符不相等，稍微更改，只会继承本字符的匹配情况，因为明确表示了该字符串是否为另一字符串的子串，不能反过来。
- 参考代码随想录思路的方法：
  ```
  class Solution {
  public:
      bool isSubsequence(string s, string t) {
          vector<vector<int>> dp(s.size()+1, vector<int>( t.size()+1, 0));
          // dp[i][j] 字符串s[i-1] 和 t[j-1] 为止最长子序列长度
          // 初始化 第一行和第一列都为 0 即可
          // 递推公式，当两个字符相等时，dp[i][j]=dp[i-1][j-1]+1;不同时，dp[i][j]则取dp[i][j-1];相当于匹配t失败，延续之前匹配结果（要么是匹配上的情况，要么是0，也就是断匹配了
          for(int i = 1; i <= s.size(); i++) {
              for (int j = 1; j <= t.size(); j++) {
                  if (s[i-1] == t[j-1]) {
                      dp[i][j] = dp[i-1][j-1] + 1;
                  }
                  else {
                      dp[i][j] = dp[i][j-1];
                  }
              }
          }
  
          return (dp[s.size()][t.size()] == s.size()) ? true : false;
      }
  };
  
  ```
- ## 不同子序列
- [代码随想录](https://www.programmercarl.com/0115.%E4%B8%8D%E5%90%8C%E7%9A%84%E5%AD%90%E5%BA%8F%E5%88%97.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [115. 不同的子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/distinct-subsequences/)
- 题意，判断一个字符串和另一个字符串的几种不同子序列相同（这里的子序列由字符串删除字符获得），求不同子序列的数量。
- 方法，是编辑距离的简单版，只考虑删除字符的情况，如果对应字符相等，就取上个字符的匹配情况
  ![image.png](../assets/image_1737950806585_0.png)  
  dp[i][j]是s[i]到t[j]匹配时，已有匹配到的数量，这个数量指的是可能出现的前缀数量，如果字符匹配成功，就继承上一个；如果之前有重复字符，则会叠加  
- 参考代码随想录思路的方法：
  ```
  class Solution {
  public:
      int numDistinct(string s, string t) {
          vector<vector<uint64_t>> dp(s.size()+1, vector<uint64_t>(t.size()+1, 0));
  
          for(int i = 0; i < s.size(); i++) {
              dp[i][0] = 1;
          }
  
          for (int i = 1; i <= s.size(); i++) {
              for (int j = 1; j <= t.size(); j++) {
                  if (s[i-1] == t[j-1]) {
                      dp[i][j] = dp[i-1][j-1] + dp[i-1][j];
                  }
                  else {
                      dp[i][j] = dp[i-1][j];
                  }
              }
          }
  
          return dp[s.size()][t.size()];
      }
  };
  ```
- ## 两个字符串的删除操作
- [代码随想录](https://www.programmercarl.com/0583.%E4%B8%A4%E4%B8%AA%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E5%88%A0%E9%99%A4%E6%93%8D%E4%BD%9C.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [583. 两个字符串的删除操作 - 力扣（LeetCode）](https://leetcode.cn/problems/delete-operation-for-two-strings/)
- 题意：给两个字符串，请给出多少次删除（一次删除任意字符串的一个字符）能使两个字符串相等。
- 方法：
	1. dp[i][j] 对于一个字符串的前 i-1 个字符和另一个字符串的前 j-1 个字符相同需要删除次数
	2. 递推公式，当字符相同时，dp[i][j] = dp[i-1][j-1]；也就是说对于这两个位置的字符不需要删除。当字符不相同时，dp[i][j] = min(dp[i-1][j]+1, dp[i][j-1]+1);也就是删除其中个字符串中的字符，那可能说，要是两个字符都要删除呢？这个状态包含于上一次内，也就是说两个删除一个字符情况也就是删除两个字符。
	3. 初始化，第一行第一列，分别是一个字符串与空字符串相等需要的删除次数，也就是删完，取字符串长度。
	4. 遍历顺序，从前向后，前上到下，普通顺序
	5. 举例……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int minDistance(string word1, string word2) {
          vector<vector<int>> dp(word1.size()+1,vector<int>(word2.size()+1, 0));
  
          for (int i = 0; i < word1.size()+1; i++) {
              dp[i][0] = i;
          }
  
          for (int j = 0; j < word2.size()+1; j++) {
              dp[0][j] = j;
          }
  
          for (int i = 1; i < word1.size()+1; i++) {
              for (int j = 1; j < word2.size()+1; j++) {
                  if (word1[i-1] == word2[j-1]) {
                      dp[i][j] = dp[i-1][j-1];
                  }
                  else {
                      dp[i][j] = min(dp[i-1][j]+1, dp[i][j-1]+1);
                  }
                  
              }
          }
  
          return dp[word1.size()][word2.size()];
      }
  };
  ```
- ## 编辑距离
- [代码随想录](https://www.programmercarl.com/0072.%E7%BC%96%E8%BE%91%E8%B7%9D%E7%A6%BB.html#%E6%80%9D%E8%B7%AF)
- [72. 编辑距离 - 力扣（LeetCode）](https://leetcode.cn/problems/edit-distance/description/)
- 题意，给两个字符串，要求编辑距离（也就是对任意字符串增删换使其相同的次数），增一个字符，删一个字符，换一个字符的操作个数。
- 方法，[[两个字符串的删除操作]]的进阶，现在不只是删除，还有增加字符和替换的操作，但是，增加字符，其实可以等效为另一个字符串删除那个要增加的字符。而替换操作，可以理解为两边字符串同时删除，但只算一次操作(dp[i][j]=dp[i-1][j-1]+1)，除了这个递推公式加上一条，其余相同。
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int minDistance(string word1, string word2) {
          vector<vector<int>> dp(word1.size()+1, vector<int>(word2.size()+1, 0));
  
          for(int i = 0; i < word1.size()+1; i++) {
              dp[i][0] = i;
          }
  
          for(int j = 0; j < word2.size()+1; j++) {
              dp[0][j] = j;
          }
  
          for (int i = 1; i < word1.size()+1; i++) {
              for (int j = 1; j < word2.size()+1; j++) {
                  if (word1[i-1] == word2[j-1]) {
                      dp[i][j] = dp[i-1][j-1];
                  }
                  else {
                      dp[i][j] = min({dp[i-1][j-1], dp[i-1][j], dp[i][j-1]})+1;
                  }
              }
          }
  
          return dp[word1.size()][word2.size()];
      }
  };
  ```
- ## 回文子串
- [代码随想录](https://www.programmercarl.com/0647.%E5%9B%9E%E6%96%87%E5%AD%90%E4%B8%B2.html#%E6%80%9D%E8%B7%AF)
- [647. 回文子串 - 力扣（LeetCode）](https://leetcode.cn/problems/palindromic-substrings/description/)
- 题意，给一个字符串，统计其中回文字符串的个数
- 方法，如果按照之前思路dp[i]代表到下标 i 回文字符串个数的话，各个状态间无联系无规律。故用下面的 dp 定义。
  除了 dp 定义有改变，还有遍历顺序与以往不太一样，由于递推公式的关系。  
  具体内容在注释中  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int countSubstrings(string s) {
          vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));
          // 这里的 dp[i][j] 是字符串 i 到 j（闭区间）是否为回文字符串、
          // 初始化默认全部为 false
  
          // 先看递推公式，当 s[i] != s[j] dp[i][j] = false;
          // 复杂的是 s[i] == s[j]时，如果 i==j，那么 dp[i][j] = true，因为”a“算回文字符串
          // 如果j==i+1，那么也是true，“aa”这种情况中也算回文字符串
          // 如果 上述都不是，那么 dp[i][j] = dp[i+1][j-1] ，i到j是否为回文字符串取决于 i+1到j-1是否为回文字符串
  
          // 由于递推公式有 dp[i][j] = dp[i+1][j-1],那么要先遍历 i 较大的情况以及 j 较小的情况
  
          for(int i = s.size()-1; i >= 0; i--) {
              for (int j = i; j < s.size(); j++) {
                  if (s[i] != s[j]) {
                      dp[i][j] = false;
                  }
                  else if (i == j) {
                      dp[i][j] = true;
                  }
                  else if (j == i + 1) {
                      dp[i][j] = true;
                  }
                  else {
                      dp[i][j] = dp[i+1][j-1];
                  }
              }
          }
  
          int count = 0;
          for (int i = 0; i < s.size(); i++) {
              for (int j = 0; j < s.size(); j++) {
                  if (dp[i][j] == true) {
                      count++;
                  }
                  //cout << dp[i][j] << " ";
              }
              //cout << endl;
          }
  
          return count;
      }
  };
  ```
- ## 最长回文子序列
- [代码随想录](https://www.programmercarl.com/0516.%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E5%BA%8F%E5%88%97.html#%E6%80%9D%E8%B7%AF)
- [516. 最长回文子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-palindromic-subsequence/description/)
- 题意：找出字符串上，最长回文子序列长度（可删字符）
- 方法：详情写在注释中，值得注意的是，dp是闭区间
- 参考代码随想录思路的解法：
  ```
  class Solution {
  public:
      int longestPalindromeSubseq(string s) {
          vector<vector<int>> dp(s.size(), vector<int>(s.size(), 0));
          // dp[i][j]代表了 s[i]到s[j] 最长回文子序列的长度
  
          // 在 s[i] == s[j] 时，dp[i][j] = dp[i+1][j-1]+2
          // 不相等时， 则从两个方向取大值，dp[i][j] = max(dp[i][j-1], dp[i+1][j]);
  
          // 初始化时，所有相等得先赋值1
          for (int i = 0; i < s.size(); i++) dp[i][i] = 1;
  
          for (int i = s.size()-1; i >= 0; i--) {
              for (int j = i+1; j < s.size(); j++) {
                  if (s[i] == s[j]) {
                      dp[i][j] = dp[i+1][j-1] + 2;
                  }
                  else {
                      dp[i][j] = max(dp[i][j-1], dp[i+1][j]);
                  }
              }
          }
  
          return dp[0][s.size()-1];
      }
  };
  ```