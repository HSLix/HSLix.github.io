---
title: 【Weekly Algorithm】算法周记之回溯（二）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-11-10 22:32:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习回溯的剩余题目，如八皇后，全排列等问题，当初看上去可怕的题目，在回溯的套路下，显得也没那么难，重点在于理解整个回溯的树遍历过程。

通常回溯部分的函数无返回值，但当回溯只求全树唯一一个解时，比如[[全排列]][[解数独]]，可将回溯函数返回值设为 bool 类型，在一个叶子为 true，层层上传，避免无用遍历。

# 回溯
- ## 组合总和II
- [代码随想录](https://programmercarl.com/0040.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8CII.html#%E6%80%9D%E8%B7%AF)
- [40. 组合总和 II - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-ii/description/)
- 题意，与[[组合总和III]]类似，但待选数组内出现重复元素，且结果的组合不能重复
- 方法，重点是分清两种去重，一种是同一树支的去重（纵向），一种是同一树层的去重（横向），两者都可以通过用 used 数组实现，前者要求 used 中同值点不可同时 true 才能加入结果，后者要求同值点必须 true 该点才能加入结果；换句话，used 中 ture 的点说明在同一树支（路径）上
- 参考代码随想录思路的解法
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
      void back_track(vector<int> &candidates, int target, vector<int> &used, int sum, int start_index) {
          // 终止条件，和过大或相等
          if (sum > target) {
              return;
          }
          if (sum == target) {
              result.push_back(path);
          }
  
          
          for (int i = start_index; i < candidates.size(); i++) {
              // 去重，used[i-1] true 说明在同一个路径上
              // used[i-1] flase 说明在同一层，属于要去除的情况
              if (i > 0 && candidates[i] == candidates[i-1] && used[i-1] == false) {
                  continue;
              }
              used[i] = true;
              path.push_back(candidates[i]);
              sum += candidates[i];
              back_track(candidates, target, used, sum, i+1);
              sum -= candidates[i];
              path.pop_back();
              used[i] = false;
          }
      }
  public:
      vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
          vector<int> used = vector<int>(candidates.size());
          result.clear();
          path.clear();
          sort(candidates.begin(), candidates.end()); // 使数组有序
          back_track(candidates, target, used, 0, 0);
          return result;
      }
  };
  ```
- ## 分割回文串
- [代码随想录](https://programmercarl.com/0131.%E5%88%86%E5%89%B2%E5%9B%9E%E6%96%87%E4%B8%B2.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [131. 分割回文串 - 力扣（LeetCode）](https://leetcode.cn/problems/palindrome-partitioning/description/)
- 题意，给一个字符串，把字符串分成多个回文串组成的字符串数组，返回所有组合
- 方法，难点有两，一是如何分割字符串，二是如何判断回文串。
  对于一，采用 start_index 作为本层分割字符串的起点，用 for 循环决定重点，递归更新 start_index；对于二，就用 for 循环前后检测是否相等，可以用动态规划优化速度，但太麻烦了，等dp学完再回来罢：）  
- 参照代码随想录思路的解法
  ```
  class Solution {
  private:
      bool isRevStr(string s) { // 判断回文串，前读和后读一样，其实前半和后半一样就行
          for (int i = 0, j = s.size()-1; i < j; i++,j--) {
              if (s[i] != s[j]) {
                  return false;
              }
          }
          return true;
      }
      vector<vector<string>> result;
      vector<string> path;
  
      void back_track(string s, int start_index) {
          if (start_index >= s.size()) { // start_index 为分割线，分割到字符串外说明前面都割好了
              result.push_back(path);
          }
  
          for (int i = start_index; i < s.size(); i++) { 
              // 从 start_index 到 i 割串，检测
              string tmp = s.substr(start_index, i-start_index+1);
              //cout << tmp << endl;
              if (isRevStr(tmp)) {
                  path.push_back(tmp);
                  back_track(s, i+1);
                  path.pop_back();
              }
          }
      }
  public:
      vector<vector<string>> partition(string s) {
          result.clear();
          path.clear();
          back_track(s, 0);
          return result;
      }
  };
  ```
- ## 复原IP地址
- [代码随想录](https://programmercarl.com/0093.%E5%A4%8D%E5%8E%9FIP%E5%9C%B0%E5%9D%80.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [93. 复原 IP 地址 - 力扣（LeetCode）](https://leetcode.cn/problems/restore-ip-addresses/description/)
- 题意，有一串数字，可能是 IP 地址，要求返回该数字可能组成所有有效的 IP 地址（最少4位、最多12位，中间有三个点分隔的数字字符串）
- 方法，与[[分割回文串]]类似，但这里限制了分割的个数，所以需要变量记录，且注意前导0和把字符串转整数的操作
- 参考代码随想录思路的解法
  ```
  class Solution {
  private:
      vector<string> result;
  
      bool isValid(const string &s, int start_index, int end_index) {
          // 满足字符串是0-255，无前导0，且是数字
          if (start_index > end_index) { // 去除最后 i+2 后 start_index 比 end 大的情况，下面代码没考虑
              return false;
          }
  
          if (s[start_index] == '0' && start_index != end_index) { // 前导0
              return false;
          }
  
          int num = 0; // 记录数值
          for (int i = start_index; i <= end_index; i++) {
              num *=10;
              char c = s[i];
              if (c < '0' || c > '9') {
                  return false;
              }
              num += int(c - '0');
              if (num > 255) {// 过大
                  return false;
              }
          }
  
          return true;
      }
  
      void backTrack(string s, int start_index, int point_num) {
          if (point_num == 3 && isValid(s, start_index, s.size()-1)) {
              result.push_back(s);
              return;
          }
          cout << s << endl;
          for (int i = start_index; i < s.size(); i++) {
              if (isValid(s, start_index, i)) {
                  s.insert(s.begin()+i+1, '.'); // 采用插点的方式来分割字符串，插入点的下标为 i+1
                  point_num++;
                  backTrack(s, i+2, point_num);
                  point_num--;
                  s.erase(s.begin()+i+1);
              }
              else {
                  break; // 要么数组过大，要么有非法字符，后面的都一定不符合
              }
          }
      }
  public:
      vector<string> restoreIpAddresses(string s) {
          result.clear();
          if (s.size() < 4 || s.size() > 12) { // ip 地址最少4位，每处最多3位，共12位
              return result;
          }
          backTrack(s, 0, 0);
          return result;
      }
  };
  ```
- ## 子集
- [代码随想录](https://programmercarl.com/0078.%E5%AD%90%E9%9B%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [78. 子集 - 力扣（LeetCode）](https://leetcode.cn/problems/subsets/description/)
- 题意，给一个整数数组，求其所有子集
- 方法，回溯，注意由于集合内无序性，元素不能重复；该题其实遍历了树上所有节点，而之前的组合、分割则是收集树中的叶子节点。
- 参考代码随想录思路的解法
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
  
      void backTrack(vector<int> &nums, int start_index) {
          if (start_index > nums.size()) {
              return;
          }
          result.push_back(path);
          for (int i = start_index; i < nums.size(); i++) {
              path.push_back(nums[i]);
              backTrack(nums, i+1);
              path.pop_back();
          }
      }
  public:
      vector<vector<int>> subsets(vector<int>& nums) {
          result.clear();
          path.clear();
          backTrack(nums, 0);
          return result;
      }
  };
  ```
- ## 子集II
- [代码随想录](https://programmercarl.com/0090.%E5%AD%90%E9%9B%86II.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [90. 子集 II - 力扣（LeetCode）](https://leetcode.cn/problems/subsets-ii/description/)
- 题意，相比[[子集]]，数组中会有重复的数字元素，求的结果子集仍然要求一样
- 方法，类似[[组合总和II]]，通过 used 去除同层同值的节点造成子集重复的问题
- 陆爻齐自己解法（与代码随想录思路相近）
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
  
      void backTrack(vector<int> &nums, int start_index, vector<int> &used) {
          // 记录结果，遍历每个节点
          result.push_back(path);
          if (start_index >= nums.size()) {
              return;
          }
  
          for (int i = start_index; i < nums.size(); i++) {
              // 排除那些同层同值的节点
              if (i>0 && nums[i] == nums[i-1] && used[i-1] == false) {
                  continue;
              }
              used[i] = true;
              path.push_back(nums[i]);
              backTrack(nums, i+1, used);
              path.pop_back();
              used[i] = false;
          }
      }
  public:
      vector<vector<int>> subsetsWithDup(vector<int>& nums) {
          result.clear();
          path.clear();
          // 需要有序才能方便去除同层相同数，不排序的话需要哈希表
          sort(nums.begin(), nums.end());
          vector<int> used = vector<int>(nums.size());
          backTrack(nums, 0, used);
          return result; 
      }
  };
  
  ```
- ## 非递减子序列
- [代码随想录](https://programmercarl.com/0491.%E9%80%92%E5%A2%9E%E5%AD%90%E5%BA%8F%E5%88%97.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [491. 非递减子序列 - 力扣（LeetCode）](https://leetcode.cn/problems/non-decreasing-subsequences/description/)
- 题意，给一个数组，要求返回其中所有至少长度为2的递增子序列
- 方法，看似与[[子集II]]相似，只要排下序，回溯即可；其实不行，因为一旦排序，整个序列都是递增的，那么如何去重呢？那就要用到 hash。不过本地的数字大小在 -100 到 100，所以用大小为 201 的数组把数字映射上去即可
- 参考代码随想录思路的代码
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
  
      void backTrack(vector<int> &nums, int start_index) {
          if (path.size() >= 2) {
              result.push_back(path); // 添加结果条件
              // 不需要 return，因为需要遍历整个树的节点
          
  
          vector<int> used = vector<int>(201); // 把-100至100两个个数，映射到0-200 的数组上
          for (int i = start_index; i < nums.size(); i++) {
              if (used[nums[i]+100] == 1) continue; // 去重
              if (path.size() > 0 && nums[i] < path[path.size()-1]) continue; // 使得新加值不小于path末值
              used[nums[i]+100] = 1; // 只管本层去重，所以记录有就行，不需要恢复0
              path.push_back(nums[i]);
              backTrack(nums, i+1);
              path.pop_back();
          }
      }
  public:
      vector<vector<int>> findSubsequences(vector<int>& nums) {
          result.clear();
          path.clear();
          backTrack(nums, 0);
          return result;
      }
  };
  
  ```
- ## 全排列
- [代码随想录](https://programmercarl.com/0046.%E5%85%A8%E6%8E%92%E5%88%97.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [46. 全排列 - 力扣（LeetCode）](https://leetcode.cn/problems/permutations/description/)
- 题意，把一个无重复数字的数组所有可能的全排列按任意顺序返回答案
- 方法，运用回溯，不过由于要求全排列，也就是可能之前的值在下面也用得到，所以不用 start_index
- 参考代码随想录思路的解法
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
  
      void backTrack(vector<int> &nums,vector<int> &used) {
          if (path.size() == nums.size()) { // 到叶子节点，说明全排列完成
              result.push_back(path);
          }
  
          for (int i = 0; i < nums.size(); i++) {
              if (used[i] == 1) continue; // 排除用过数字的情况
              used[i] = 1;
              path.push_back(nums[i]);
              backTrack(nums, used);
              path.pop_back();
              used[i] = 0;
          }
      }
  public:
      vector<vector<int>> permute(vector<int>& nums) {
          result.clear();
          path.clear();
          vector<int> used = vector<int>(nums.size());
          backTrack(nums, used);
          return result
      }
  };
  
  ```
- ## 全排列II
- [代码随想录](https://programmercarl.com/0047.%E5%85%A8%E6%8E%92%E5%88%97II.html#%E6%80%9D%E8%B7%AF)
- [47. 全排列 II - 力扣（LeetCode）](https://leetcode.cn/problems/permutations-ii/)
- 题意，与[[全排列]]类似，但数组中出现重复元素
- 方法，在[[全排列]]的基础上，加入去重，不过这个去重其实在树枝去重和树层去重都是可以的，但对树层去重效率会高很多，可以举个【1，1，1】的例子试试
- 参考代码随想录思路的代码
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
      void backTrack(vector<int> &nums, vector<int> &used) {
          if (path.size() == nums.size()) {
              result.push_back(path);
              return;
          }
  
          for (int i = 0; i < nums.size(); i++) {
              if (i>0 && nums[i] == nums[i-1] && used[i-1]==0) continue;
              if (used[i] == 0) {
                  used[i] = 1;
                  path.push_back(nums[i]);
                  backTrack(nums, used);
                  path.pop_back();
                  used[i] = 0;
              }
          }
      }
  public:
      vector<vector<int>> permuteUnique(vector<int>& nums) {
          result.clear();
          path.clear();
          vector<int> used(nums.size());// 映射这个值是否用过
          sort(nums.begin(), nums.end());
          backTrack(nums, used);
          return result;
      }
  };
  
  ```
- ## 重新安排行程
- [代码随想录](https://programmercarl.com/0332.%E9%87%8D%E6%96%B0%E5%AE%89%E6%8E%92%E8%A1%8C%E7%A8%8B.html#%E6%80%9D%E8%B7%AF)
- [332. 重新安排行程 - 力扣（LeetCode）](https://leetcode.cn/problems/reconstruct-itinerary/description/)
- 题意，给一系列大小为 2 的字符串数组，当作机票的出发地和目的地，要求从 JFK 出发，每张票要用且只能使用一次，要求最后返回一条有效路程，若有多个有效路程则返回字典序最小的那条
- 方法，主要是如何在回溯过程中记录回溯历程，这里使用了一个 unordered_map<string, map<string, int>> 来记录，翻译为自然语言，就是 unordered_map<出发地, map<目的地，票数>>，map是有序的，直接遍历就是字典序从小到大
- 参考代码随想录思路的方法
  ```
  class Solution {
  private:
      vector<string> result;
  
      unordered_map<string,map<string, int>> rec_tic;
  
      bool backTrack(int ticket_num) {
          if (ticket_num + 1 == result.size()) {
              return true;
          }
  
          map<string, int> &target = rec_tic[result[result.size()-1]];
          for (pair<const string, int> &travel : target) {
              if (travel.second > 0) {
                  travel.second--;
                  result.push_back(travel.first);
                  if (backTrack(ticket_num)) return true;
                  result.pop_back();
                  travel.second++;
              }
          }
          return false;
      }
  public:
      vector<string> findItinerary(vector<vector<string>>& tickets) {
          result.clear();
          rec_tic.clear();
  
          for (vector<string> tic : tickets) {
              rec_tic[tic[0]][tic[1]]++;
          }
          result.push_back("JFK");
          backTrack(tickets.size());
          return result;
      }
  };
  
  ```
- ## N皇后
- [代码随想录](https://programmercarl.com/0051.N%E7%9A%87%E5%90%8E.html#%E6%80%9D%E8%B7%AF)
- [51. N 皇后 - 力扣（LeetCode）](https://leetcode.cn/problems/n-queens/)
- 题意，国际棋盘上，皇后这个棋子同行、同列、两斜线上都是唯一，给棋盘大小，要求摆皇后的所有方案
- 方法，把行作为递归深度，到底就是得出结果，然后代入回溯模板即可，由于在二维数组上（棋盘）从上往下遍历，故检查下棋位置是否合法只要检测上面即可
- 参考代码随想录思路的方法，时隔许久再做，好像也没那么恐怖哈
  ```
  class Solution {
  private:
      vector<vector<string>> result;
      vector<string> chess_board;
  
      bool isValid(int row, int col, int n) {
  
          // 检查同列
          for (int i = 0; i < row; i++) {
              if (chess_board[i][col] == 'Q') {
                  return false;
              }
          } 
          // 由于由上向下，故向上检查斜角即可
          // 检查左上方的，45度
          for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
              if (chess_board[i][j] == 'Q') {
                  return false;
              }
          }
  
          // 检查右上方，135度
          for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
              if (chess_board[i][j] == 'Q') {
                  return false;
              }
          }
  
          return true;
      }
  
      void backTrack(int n, int row) {
          if (row == n) {
              result.push_back(chess_board);
              return;
          }
  
          for (int col = 0; col < n; col++) {
              if (isValid(row, col, n)) {
                  chess_board[row][col] = 'Q';
                  backTrack(n, row+1);
                  chess_board[row][col] = '.';
              }
          }
      }
  public:
      vector<vector<string>> solveNQueens(int n) {
          result.clear();
          chess_board.clear();
          chess_board = vector<string>(n, string(n, '.'));
          backTrack(n, 0);
          return result;
      }
  };
  
  ```
- ## 解数独
- [代码随想录](https://programmercarl.com/0037.%E8%A7%A3%E6%95%B0%E7%8B%AC.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [37. 解数独 - 力扣（LeetCode）](https://leetcode.cn/problems/sudoku-solver/submissions/579312550/)
- 题意，给出一个二维数组，其中待填部分为 .，要求填入合适数字完成数独
- 方法，和[[八皇后]]类似的棋盘问题，也是需要二重回溯，区别就是在判断终止条件，由于该题需要遍历一整棵树找一个合理方案，故让回溯方法返回布尔值，以去除不必要的遍历。
- 参考代码随想录思路的解法
  ```
  class Solution {
  private:
      bool isValid(int row, int col, char k, vector<vector<char>> &board, int size) {
          for (int i = 0; i < size; i++) { // 同列是否有相同值
              if (board[i][col] == k) {
                  return false;
              }
          }
  
          for (int j = 0; j < size; j++) { // 同行是否有相同值
              if (board[row][j] == k) {
                  return false;
              }
          }
  
          int start_row = row/3*3; // 同九方格起始坐标
          int start_col = col/3*3;
          for (int i = start_row; i < start_row + 3; i++) {
              for (int j = start_col; j < start_col + 3; j++)
              if (board[i][j] == k) {
                  return false;
              }
          }
  
          return true;
      }
  
      bool backTrack(vector<vector<char>> &board, int size) {
          for (int i = 0; i < size; i++) {
              for (int j = 0; j < size; j++) {
                  if (board[i][j] != '.') continue;
                  for (char k = '1'; k <= '9'; k++) {
                      if (isValid(i, j, k, board, size)) {
                          board[i][j] = k;
                          if (backTrack(board, size)) return true;
                          board[i][j] = '.';
                      }
                  }
                  return false; // 如果有一个可填处 9 个值都填不了，那么这个方案不可行
              }
          }
          return true; // 如果全填完了，说明必定出现答案了
      }
  
  public:
      void solveSudoku(vector<vector<char>>& board) {
          backTrack(board, board.size());
      }
  };
  
  ```