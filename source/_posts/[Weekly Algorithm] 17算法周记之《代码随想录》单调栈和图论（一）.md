---
title: 【Weekly Algorithm】算法周记之《代码随想录》单调栈和图论（一）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
date: 2024-02-09 19:12:00
---
# 学习小结
本周跟随《代码随想录》学习关于单调栈的知识，同时开启图论。

单调栈通过保持一个栈从栈顶到栈底的递增或是递减序列，来解决找一个元素左/右的“第一个”更大/更小元素。

图论则是初窥dfs的门路，虽然道理一年前早在算法课学过考过，但现在也得复习才能捡回来。

# 单调栈
- ## 每日温度
- [代码随想录](https://www.programmercarl.com/0739.%E6%AF%8F%E6%97%A5%E6%B8%A9%E5%BA%A6.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [739. 每日温度 - 力扣（LeetCode）](https://leetcode.cn/problems/daily-temperatures/)
- 题意：给一个整数数组，求每个数字右侧比该数字更大的间距
- 方法：暴力解法就是O(n^2)的遍历。更高效率的方法是使用单调栈，所谓的单调栈就是从栈顶到栈底递增或递减的，如此排列，就能方便找任意元素的左右第一个更大更小的元素。
  
  比如本题是找右边更大的，那么遍历数组时，遇到更小或相等的元素就直接入栈，而遇到更大的元素就弹栈，直到触底或碰到大元素再入栈，其中弹栈的元素就能记录本元素的下标与当时元素下标之差。  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<int> dailyTemperatures(vector<int>& temperatures) {
          vector<int> result(temperatures.size(), 0);
  
          stack<int> st;
  
          st.push(0);
  
          for (int i = 1; i < temperatures.size(); i++) {
              if (temperatures[i] <= temperatures[st.top()]) {
                  st.push(i);
              }
              else {
                  while(!st.empty() && temperatures[i] > temperatures[st.top()]) {
                      result[st.top()] = i - st.top();
                      st.pop();
                  }
                  st.push(i);
              }
          }
  
          return result;
      }
  };
  ```
- ## 下一个更大的元素I
- [代码随想录](https://www.programmercarl.com/0496.%E4%B8%8B%E4%B8%80%E4%B8%AA%E6%9B%B4%E5%A4%A7%E5%85%83%E7%B4%A0I.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [496. 下一个更大元素 I - 力扣（LeetCode）](https://leetcode.cn/problems/next-greater-element-i/)
- 题意：给两个整数数组，找一个数组在另一个数组对应元素的下一个更大元素的集合。
- 方法：用哈希建立两个数组元素和下标的对应，单调栈则实现元素的下一个更大的元素。
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
          // 按照代码随想录思路，用哈希表构建两数组之间的映射，然后得出nums2单调栈，由此就得到了结果
          unordered_map<int, int> umap; // 从nums2数字到nums1对应数字下标的映射
          for (int i = 0; i < nums1.size(); i++) {
              umap[nums1[i]] = i;
          }
          
          // 求nums2单调栈
          // vector<int> next(nums2.size(), -1);
          stack<int> st;
          vector<int> result(nums1.size(), -1);
          if (result.size() == 1) return result;
          st.push(0);
  
          for(int i = 1; i < nums2.size(); i++) {
              if (nums2[i] <= nums2[st.top()]) {
                  st.push(i);
              }
              else {
                  while(!st.empty() && nums2[i] > nums2[st.top()]) {
                      // next[st.top()] = i - st.top();
                      if (umap.count(nums2[st.top()]) > 0) {
                          result[umap[nums2[st.top()]]] = nums2[i];
                      }
                      st.pop();
                  }
                  st.push(i);
              }
          }
  
          return result;
      }
  };
  ```
- ## 下一个更大的元素II
- [代码随想录](https://www.programmercarl.com/0503.%E4%B8%8B%E4%B8%80%E4%B8%AA%E6%9B%B4%E5%A4%A7%E5%85%83%E7%B4%A0II.html#%E6%80%9D%E8%B7%AF)
- [503. 下一个更大元素 II - 力扣（LeetCode）](https://leetcode.cn/problems/next-greater-element-ii/description/)
- 题意：和[[下一个更大的元素I]]相似，但是下一个更大元素要看循环数组。
- 方法：解决循环以往就有把数组循环乘二的情况，但是这种处理方式对于空间稍显浪费，所以还有一种方式，就是循环时当作两倍的情况。
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<int> nextGreaterElements(vector<int>& nums) {
          vector<int> result(nums.size(), -1);
  
          stack<int> st;
          st.push(0);
  
          for (int i = 0; i < nums.size()*2; i++) {
              if (nums[i % nums.size()] <= nums[st.top()]) {
                  st.push(i % nums.size());
              }
              else {
                  while(!st.empty() && nums[i % nums.size()] > nums[st.top()]) {
                      result[st.top()] = nums[i % nums.size()];
                      st.pop();
                  }
                  st.push(i % nums.size());
              }
          }
  
          return result;
      }
  };
  ```
- ## 接雨水
- [代码随想录](https://www.programmercarl.com/0042.%E6%8E%A5%E9%9B%A8%E6%B0%B4.html#%E6%80%9D%E8%B7%AF)
- [42. 接雨水 - 力扣（LeetCode）](https://leetcode.cn/problems/trapping-rain-water/)
- 题意，给一个高度数组，每个数字代表柱子高度，求下完雨后，柱子之间积水量
- 方法，暴力解法，按行或列求，以按列为例，对每一列求其左右最高柱子高度，取最小值，与该列高度差为该列水量。优化方法是用双指针，分别从两侧向另一侧遍历，求每个柱子一侧方向的最高柱子，这样就避免了暴力方法中重复求两侧柱子高度的浪费；
  
  重点在于单调栈方法，按行求，单调栈保持递增（栈顶到栈底），具体处理方法写在注释中  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int trap(vector<int>& height) {
          stack<int> st;
          int sum = 0;
          st.push(0);
          for (int i = 1; i < height.size(); i++) {
              // 三种情况
              // 第一种，后续柱子高度低于栈顶，则直接入栈
              if (height[i] < height[st.top()]) {
                  st.push(i);
              }
              // 第二种，后续柱子高度与栈顶一致，则更新栈顶，坐标保留靠右的
              // 因为如果右侧有凹陷，计算积水的左侧左边起始是从靠右的柱子开始计算
              else if (height[i] == height[st.top()]) {
                  st.pop();
                  st.push(i);
              }
              // 第三种，后续柱子比栈顶高，开始计算积水
              // 总得来说，就是计算i，栈顶和栈顶前一个柱子三个柱子之间的积水
              // 高度上，取左右两柱子的最小值，宽度上，就计算左右柱子的间隔就好
              // 上述步骤循环，直至新柱子小于栈顶位置再入栈（或者栈空）
              // 计算完成后，左侧柱子在栈顶位置，还有计算价值，毕竟说不定后续用作底部柱子使用
              else {
                  while(!st.empty() && height[i] > height[st.top()]) {
                      int bottom = height[st.top()];
                      st.pop();
                      if (!st.empty()) { // 防止遇到没有左侧柱子的情况
                          int h = min(height[i], height[st.top()]) - bottom;
                          int w = i - st.top() - 1;
                          sum += h*w;
                      }
                      
                  }
                  st.push(i);
              }
          }
  
          return sum;
      }
  };
  ```
- ## 柱状图中最大的矩形
- [代码随想录](https://programmercarl.com/0084.%E6%9F%B1%E7%8A%B6%E5%9B%BE%E4%B8%AD%E6%9C%80%E5%A4%A7%E7%9A%84%E7%9F%A9%E5%BD%A2.html#%E6%80%9D%E8%B7%AF)
- [84. 柱状图中最大的矩形 - 力扣（LeetCode）](https://leetcode.cn/problems/largest-rectangle-in-histogram/submissions/597563485/)
- 题意：给一个柱状图（数组表示）求其中最大矩形面积
- 方法：实际上与[[接雨水]]相似，但是单调栈方向相反，[[接雨水]]栈顶到栈底递增，如此就能对于每个柱子找到旁边更大的柱子；这里则是递减，找到更小的柱子，以两侧小柱子为下标区间，乘以基准柱子高度，就是一块矩形面积
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int largestRectangleArea(vector<int>& heights) {
          heights.insert(heights.begin(), 0);
          heights.push_back(0);
          stack<int> st;// 存储下标
          int result = 0;
          
          st.push(0);
          for (int i = 1; i < heights.size(); i++) {
              if (heights[st.top()] < heights[i]) {
                  st.push(i);
              }
              else if (heights[st.top()] == heights[i]) {
                  st.pop();
                  st.push(i);
              }
              else {
                  while(!st.empty() && heights[st.top()] > heights[i]) {
                      int mid = st.top();
                      st.pop();
                      if (!st.empty()) {
                          int left = st.top();
                          int right = i;
                          int w = right - left - 1;
                          int h = heights[mid];
                          result = max(result, h * w);
                      }
                  }
                  st.push(i);
              }
          }
  
          return result;
      }
  };
  ```
# 图论
- ## 所有可达路径
- [代码随想录](https://www.programmercarl.com/kamacoder/0098.%E6%89%80%E6%9C%89%E5%8F%AF%E8%BE%BE%E8%B7%AF%E5%BE%84.html#%E6%9C%AC%E9%A2%98%E4%BB%A3%E7%A0%81)
- [98. 所有可达路径](https://kamacoder.com/problempage.php?pid=1170)
- 题意：给一个无环有向图，要求一个初次节点到末尾节点的所有路径
- 方法：dfs，重点在于具体的实现方法，注意邻接矩阵的vector长度，是n+1，因为题目的编号是1-n。还有题目的输出示例格式要求。
- 参考代码随想录思路的解法
  ```
  #include<iostream>
  #include<vector>
  
  using namespace std;
  
  vector<vector<int>> result;
  vector<int> path;
  
  void dfs(vector<vector<int>> &g, int vis, int end) {
      if (vis == end) {
          result.push_back(path);
          // cout << "path:" << endl;;
          // for (int i = 0; i < path.size(); i++) {
          //     cout << path[i] << " ";
          // }
          // cout << endl;
          return;
      }
      for (int i = 1; i <= end; i++) {
          if (g[vis][i] == 1) {
              path.push_back(i);
              dfs(g, i, end);
              path.pop_back();
          }
      }
  }
  
  int main() {
      
      int n, m;
      cin >> n >> m;
      
      // 用邻接矩阵来存储看看
      vector<vector<int>> g(n+1, vector<int>(n+1, 0));
      
      for (int i = 0; i < m; i++) {
          int s, t;
          cin >> s >> t;
          g[s][t] = 1;
      }
      
      path.push_back(1);
      dfs(g, 1, n);
      
      if (result.size() == 0) {
          cout << "-1" << endl;
      }
      
      for (int i = 0; i < result.size(); i++) {
          vector<int> &t = result[i];
          for (int j = 0; j < t.size()-1; j++) {
              cout << t[j] << " ";
          }
          cout << t[t.size()-1] << endl;
      }
      
      return 0;
  }
  ```
- ## 岛屿数量深搜版
- [代码随想录](https://www.programmercarl.com/kamacoder/0099.%E5%B2%9B%E5%B1%BF%E7%9A%84%E6%95%B0%E9%87%8F%E6%B7%B1%E6%90%9C.html#%E6%80%9D%E8%B7%AF)
- [99. 岛屿数量](https://kamacoder.com/problempage.php?pid=1171)
- 题意：给一个二维数组，数组中横纵相连视为一个岛，求岛屿数量
- 方法：dfs，模板题，重点在于注意数组的存储
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1};
  void dfs(const vector<vector<int>> &grid, vector<vector<bool>> &visited, int x, int y) {
      for (int i = 0; i < 4; i++) {
          int nextx = x + dir[i][0];
          int nexty = y + dir[i][1];
          if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;
          if (!visited[nextx][nexty] && grid[nextx][nexty] == 1) {
              visited[nextx][nexty] = true;
              dfs(grid, visited, nextx, nexty);
          }
      }
  }
  
  int main() {
      int n, m;
      cin >> n >> m;
      vector<vector<int>> grid(n, vector<int>(m, 0));
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      vector<vector<bool>> visited(n, vector<bool>(m, false));
      
      int result = 0;
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (!visited[i][j] && grid[i][j] == 1) {
                  visited[i][j] = true;
                  result++;
                  dfs(grid, visited, i, j);
              }
          }
      }
      cout << result << endl;
      return 0;
  }
  ```