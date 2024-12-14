---
title: 【Weekly Algorithm】算法周记之《代码随想录》动态规划（一）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 875acdbe
date: 2024-11-17 11:50:00
---
# 学习小结
本周跟随《代码随想录》开始学习动态规划的相关题目，从基础的斐波那契数到爬楼梯，再到 01 背包问题，慢慢熟悉动态规划的五步法。
《代码随想录》还提醒，调试重点是检查递推公式是否举例，检查打印 dp 数组是否和自己设想一致。
1. 分析 dp 数组及其下标含义
2. 确定递推公式
3. 确定 dp 数组初始化
4. 确定遍历顺序
5. 举例分析 dp 数组是否如所想迭代

此外除了寻常的二维数组动态规划，也尝试做滚动数组的练习。

# 动态规划
- ## 斐波那契数
- 题意，给一个数字 n，求斐波那契数中的第 n 个数
- 方法，虽然就算不看代码随想录也能轻易写出，但简单题的重点在于熟悉套路方法，下面按五步分析
	1. 确定 dp 数组及其下标含义，这里的 dp[i] 代表斐波那契数中的第 i 个数
	2. 确认递推公式，题目给出 `dp[i]=dp[i-1]+dp[i-2]`
	3. dp 数组初始化，题目也给出`dp[0]=0;dp[1]=1;`
	4. 确定遍历顺序，从递推公式可见，就是从前向后（0-n）遍历
	5. 举例推导 dp 公式，0 1 1 2 3 5 8 13 21 ……

- 陆爻齐的解
  ```
  class Solution {
  public:
      int fib(int n) {
          if (n <= 1) return n;
          int dp[n+1]; // dp数组为斐波那契数列的存储
          dp[0] = 0; // 初始化dp数组
          dp[1] = 1;
          for (int i = 2; i <= n; i++) {
              dp[i] = dp[i-1] + dp[i-2]; // 递推公式
          }
          return dp[n];
      }
  };
  
  ```
- ## 爬楼梯
- [代码随想录](https://programmercarl.com/0070.%E7%88%AC%E6%A5%BC%E6%A2%AF.html#%E6%80%9D%E8%B7%AF)
- [70. 爬楼梯 - 力扣（LeetCode）](https://leetcode.cn/problems/climbing-stairs/description/)
- 题目，设定你一次能爬 1 或 2 层台阶，给你 n 个台阶，求上去的方式数量
- 方法，简单举例可见，上第 3 层台阶是根据第 1 层台阶和第 2 层台阶的状态决定的，那么就按五步法
	1. 确认 dp 数组及其下标含义，这里的 dp[i] 表示上到第 i 个台阶的方法；
	2. 确认递推公式，从上面简单举例可见，dp[i]=dp[i-1]+dp[i-2];也就是从 i-1 上一阶和 i-2 上两阶，若 i-2 上一阶就到 i-1 而不是 i 了；
	3. dp 数组初始化，由于题目也没说 0 层的初始化，那么直接初始化 dp[1]=1;dp[2]=2;应当是无异议的；
    4. 确认遍历顺序，爬楼梯也是从前往后的  
	5. 举例推导 dp 数组， 1 2 3 5 8 13 ……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int climbStairs(int n) {
          int dp[n+1]; // 爬到第 i 楼梯的方法数
          if (n <= 2) return n;
          dp[1]=1;
          dp[2]=2;
          for (int i = 3; i <= n; i++) {
              dp[i] = dp[i-1] + dp[i-2]; // 递推式
          } 
          return dp[n];
      }
  };
  
  ```
- ## 使用最小花费爬楼梯
- [代码随想录](https://programmercarl.com/0746.%E4%BD%BF%E7%94%A8%E6%9C%80%E5%B0%8F%E8%8A%B1%E8%B4%B9%E7%88%AC%E6%A5%BC%E6%A2%AF.html#%E6%80%9D%E8%B7%AF)
- [746. 使用最小花费爬楼梯 - 力扣（LeetCode）](https://leetcode.cn/problems/min-cost-climbing-stairs/)
- 题意，类似[[爬楼梯]]，但从一级楼梯向上爬一或二级都会消耗体力值，要求爬到顶的最小花费
- 方法，五步法
	1. 确认 dp 数组及其下标含义，dp[i] 是到达第 i 阶的最小体力花费
	2. 确认递推公式，dp[i] = min(dp[i-1]+cost[i-1], dp[i-2]+cost[i-2])
	3. 确认初始化，由于上第一级或第二级不需要体力值，dp[0]=0;dp[1]=1;
	4. 确定遍历顺序，爬楼梯从前往后
	5. 举例，……

- 参考代码随想录思路解法
  ```
  class Solution {
  public:
      int minCostClimbingStairs(vector<int>& cost) {
          int dp[cost.size()+1]; // dp[i]到达第i阶所需最小体力
          dp[0] = 0; dp[1] = 0; // 初始化，第一步不用体力
          for (int i = 2; i < cost.size()+1; i++) { // 遍历顺序从前到后
              dp[i] = min(dp[i-2]+cost[i-2], dp[i-1]+cost[i-1]); // 递推公式，可从下两阶上两阶或下一阶上一阶
          }
          return dp[cost.size()];
  
      }
  };
  
  ```
- ## 不同路径
- [代码随想录](https://programmercarl.com/0062.%E4%B8%8D%E5%90%8C%E8%B7%AF%E5%BE%84.html#%E6%80%9D%E8%B7%AF)
- [62. 不同路径 - 力扣（LeetCode）](https://leetcode.cn/problems/unique-paths/description/)
- 题意，有一个 [m][n] 的数组，问题从左上角只能向右或向下移动，右下角有几条路径
- 方法，五步法
	1. 确认 dp 数组及其下标含义，dp[i][j] 表示到达 i，j 的路径和

    2. 确认递推式，由于一个格子可由上或左来，故 dp[i][j] = dp[i-1][j]+dp[i][j-1];  
	3. 确认初始化，陆爻齐在递推过程中判别了边界，所以只初始化 dp[0][0]=1; 不过要是不想在中间做判别，可以把边缘初始化了在递推，即 dp[0][*]=0;dp[*][0]=0;（这里的 * 泛指数字）
	4. 确认遍历顺序，从上向下，从左向右
	5. 举例……

- 陆爻齐的解法
  ```
  class Solution {
  public:
      int uniquePaths(int m, int n) {
          vector<vector<int>> dp = vector(m,vector<int>(n, 0)); // dp[i][j] 代表从左上角到 i，j 的路径数
          dp[0][0] = 1; // 由于递推是取上和左路径和，故得取 1
          for (int i = 0; i < m; i++) { // 从上往下
              for (int j = 0; j < n; j++) { // 从左往右
                  if (i > 0) {
                      dp[i][j] += dp[i-1][j];
                  }
                  if (j > 0) {
                      dp[i][j] += dp[i][j-1];
                  }
              }
          }
          return dp[m-1][n-1];
      }
  };
  
  ```
  参考滚动数组的改进，即二维数组转一维数组  
  ```
  class Solution {
  public:
      int uniquePaths(int m, int n) {
          vector<int> dp = vector<int>(n, 0); // dp[j] 代表从左上角第 i 轮循环到 i，j 的路径数
          dp[0] = 1; // 由于递推是取上和左路径和，故得取 1
          for (int i = 0; i < m; i++) { // 从上往下
              for (int j = 0; j < n; j++) { // 从左往右
                  if (j > 0) {
                      dp[j] += dp[j-1];
                  }
              }
          }
          return dp[n-1];
      }
  };
  
  ```
  代码随想录的解法  
  ```
  class Solution {
  public:
      int uniquePaths(int m, int n) {
          vector<vector<int>> dp(m, vector<int>(n, 0));
          for (int i = 0; i < m; i++) dp[i][0] = 1;
          for (int j = 0; j < n; j++) dp[0][j] = 1;
          for (int i = 1; i < m; i++) {
              for (int j = 1; j < n; j++) {
                  dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
              }
          }
          return dp[m - 1][n - 1];
      }
  };
  ```
- ## 不同路径II
- [代码随想录](https://programmercarl.com/0063.%E4%B8%8D%E5%90%8C%E8%B7%AF%E5%BE%84II.html#%E6%80%9D%E8%B7%AF)
- [63. 不同路径 II - 力扣（LeetCode）](https://leetcode.cn/problems/unique-paths-ii/description/)
- 题意，与[[不同路径]]类似，不过数组中可能有障碍物
- 方法，也类似，不过对于障碍物的那一格，就设置为零
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
          int m = obstacleGrid.size();
          int n = obstacleGrid[0].size();
          vector<vector<int>> dp(m, vector<int>(n,0)); // dp[i][j] 为从 0，0 到 i，j 的路径数和
          
          for (int i = 0; i < m && obstacleGrid[i][0] == 0; i++) {
              dp[i][0] = 1;
          }
  
          for (int j = 0; j < n && obstacleGrid[0][j] == 0; j++) {
              dp[0][j] = 1;
          }
  
          for (int i = 1; i < m; i++) {
              for (int j = 1; j < n; j++) {
                  if (obstacleGrid[i][j] == 1) continue; // 障碍物处路线为 0
                  dp[i][j] += dp[i-1][j] + dp[i][j-1];
              }
          }
          return dp[m-1][n-1];
      }
  };
  
  ```
- ## 整数拆分
- [代码随想录](https://programmercarl.com/0343.%E6%95%B4%E6%95%B0%E6%8B%86%E5%88%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [343. 整数拆分 - 力扣（LeetCode）](https://leetcode.cn/problems/integer-break/description/)
- 题意，给一个正整数，求其拆分为多个(大于等于2)正整数的最大乘积
- 方法，
	1. 确认 dp[i] 的含义，为对 i 拆分多个正整数的最大乘积
	2. 确认递推式，dp[i] = max(j * (i-j), j * dp[i-j], dp[i])，一是普通情况，拆分两数，三是原本值，二则是利用历史乘积，为拆分多个值的乘积
	3. 确认初始化，dp[0], dp[1] = 0, 0; dp[2] = 1；起码拆两数
	4. 遍历顺序，从前往后，虽然前面这些都是前->后，不过后面有些就不是了
	5. 举例……

- 参考代码随想录思路的代码
  ```
  class Solution {
  public:
      int integerBreak(int n) {
          vector<int> dp(n+1, 0); // dp[i] 表示对 i 拆分后的最大乘积
          dp[0] = 0; dp[1] = 0; // 这两没法拆两正整数
          dp[2] = 1;
  
          for (int i = 3; i <= n; i++) {
              for (int j = 1; j <= i/2; j++) {
                  // 第一个是拆分为 2 个，第二个是拆分为大于 2 个的情况的乘积
                  dp[i] = max(j * (i-j), max(j * dp[i-j], dp[i]));
              }
          }
  
          return dp[n];
      }
  };
  
  ```
- ## 不同的二叉搜索树
- [代码随想录](https://programmercarl.com/0096.%E4%B8%8D%E5%90%8C%E7%9A%84%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.html#%E6%80%9D%E8%B7%AF)
- [96. 不同的二叉搜索树 - 力扣（LeetCode）](https://leetcode.cn/problems/unique-binary-search-trees/)
- 问题，给你数字 n，要求返回 n 个节点能组成的二叉搜索树个数
- 方法，一开始难以下手，总之先看看例子，n = 0时，无节点算一个；n=1时，也算一个；n=2时，算两个，如下图所示
  ![image.png](../assets/image_1731631111478_0.png)  
  n=3时，有五个，如下图所示  
  ![image.png](../assets/image_1731631134171_0.png)  
  可见，n = 3中，其实可分三类，左边两个为一类，是左子树 0 节点，右子树 2 节点，2 * 1 有 2 种； 中间一类，左子树和右子树各 1 节点，有 1 * 1 共 1 种； 右侧一类，左子树 2 节点，右子树 0 节点，有 2 * 1 共 2 种。一共五种。  
- 五步法：
	1. dp[i] 是 i 个节点组成的二叉搜索树的种类数
	2. 递推公式： dp[i] += dp[j-1]*dp[i-j]; 右边 i-1 个点，符合题意，毕竟固定有个点放根，不参与左右子树的方案
	3. 初始化，0 个节点二叉搜索树有 1 个，dp[0] = 1;
	4. 遍历顺序，从前往后
	5. 举例 ……

- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int numTrees(int n) {
          vector<int> dp(n+1, 0); // dp[i] 是 i 个节点组成的二叉搜索树的种类数
          dp[0] = 1; // 0个节点算二叉搜索树、
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j <= i; j++) {
                  dp[i] += dp[j-1] * dp[i-j]; // 左子树方案数 * 右子树方案数
              }
          }
  
          return dp[n];
      }
  };
  
  ```
- ## 01 背包理论
- [代码随想录](https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-1.html#%E6%80%9D%E8%B7%AF)
- [46. 携带研究材料（第六期模拟笔试）](https://kamacoder.com/problempage.php?pid=1046)
- 题意，01 背包通常不会直接考，更多时候是把情景题转化为 01 背包问题，正如研究材料中，研究材料价值和重量也对应 01 背包
- 方法，
	1. 确认 dp数组以及下标含义，dp[i][j] 的意思是当背包大小为 j，从0-i下标材料任选组合价值最大的值
	2. 确认递推公式, 若剩余重量不足 dp[i][j] = dp[i-1][j] ,剩余重量充足就是 dp[i][j] = max(dp[i-1][j], dp[i-1][j-weigh[i]]+val[i])，也就是不加入当前物品的最大价值和加入当前物品的价值与剩余重量最大价值之和相比较。
	3. 初始化，第一行和第一列都下应先为 0，再判断第一个物品在第一行是否能放入
	4. 遍历顺序，先遍历物品，其中遍历背包容量
	5. 举例……

- 但此题还有一点，那就是滚动数组的解法，可以将二维数组转为一维数组，降低空间复杂度，具体可继续按上述五步法
	1. 确定 dp 数组及其下标含义，dp[j] 的意思是背包空间为 j 时的最大价值容量
	2. 递推公式，由于压缩后，每层遍历相当于新值与原坐标的数值比较，dp[j] = max(dp[j], dp[j-weigh[i]] + val[i])
	3. 初始化，dp[*]=0,毕竟可从第一个物品开始做起
	4. 遍历顺序，这是该方法重点，必须在遍历空间时倒序，也就是 j 的值必须先取大值到小，从数组的角度，就是从右向左。原因是，每个值的更新可能与其更小坐标相关，如果先更新了小坐标，那么就会影响到大坐标的更新。
	5. 举例……

- 参考代码随想录思路的二维数组解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      // init
      int m, n; // 材料个数和背包大小
      cin >> m >> n;
      vector<int> weigh(m);// 分别是所占空间和价值
      vector<int> val(m);
      for (int i = 0; i < m; i++) {
          cin >> weigh[i];
      }
      for (int i = 0; i < m; i++) {
          cin >> val[i];
      }
      
      vector<vector<int>> dp(m, vector<int>(n+1, 0)); 
      // dp[i][j] 为当背包大小为 j，从0-i下标材料任选组合价值最大的值
      // dp[0][*]和dp[*][0]皆已初始化为0，还差第一个材料的初始化
      for (int i = 0; i <= n; i++) {
          if (i >= weigh[0]) dp[0][i] = val[0];
      }
      
      // 递推顺序，物品中遍历背包容量（本题反过来貌似也可）
      for (int i = 1; i < m; i++) {
          for (int j = 1; j <= n; j++) {
              if (j < weigh[i]) dp[i][j] = dp[i-1][j];
              else dp[i][j] = max(dp[i-1][j], dp[i-1][j-weigh[i]] + val[i]);
          }
      }
      /*
      for (int i = 0; i < m; i++) {
          for (int j = 0; j < n+1;j++) {
              cout << dp[i][j] << " ";
          }
          cout << endl;
      }
      cout << endl;
      */
      cout << dp[m-1][n] << endl;
      
      return 0;
  }
  ```
  滚动数组的解法  
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      // init
      int m, n; // 材料个数和背包大小
      cin >> m >> n;
      vector<int> weigh(m);// 分别是所占空间和价值
      vector<int> val(m);
      for (int i = 0; i < m; i++) {
          cin >> weigh[i];
      }
      for (int i = 0; i < m; i++) {
          cin >> val[i];
      }
      
      vector<int> dp(n+1, 0); 
      // dp[j] 为当背包大小为 j，从0-i下标材料任选组合价值最大的值
      // dp[*]皆已初始化为0
  
      
      // 递推顺序，物品中遍历背包容量（本题反过来貌似也可）
      for (int i = 0; i < m; i++) {
          for (int j = n; j > 0; j--) {
              if (j >= weigh[i]) dp[j] = max(dp[j] ,dp[j-weigh[i]] + val[i]);
              /*
              for (int j = 0; j < n+1;j++) {
                  cout << dp[j] << " ";
              }
              cout << endl;
              */
          }
      }
      
      
      
      cout << dp[n] << endl;
      
      return 0;
  }
  ```