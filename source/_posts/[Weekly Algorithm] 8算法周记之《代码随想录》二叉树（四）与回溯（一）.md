---
title: 【Weekly Algorithm】算法周记之《代码随想录》二叉树（四）与回溯（一）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 20b866e1
date: 2024-11-03 10:31:00
---
# 学习小结
本周跟随《代码随想录》学习二叉树和二叉搜索树的相关题目，还在递归中主要学习了关于组合的一部分题目。

回溯，用代码随想录中的一句话概括：- for 循环横向遍历，递归纵向遍历，回溯调整结果集。


# 二叉树
- ## 二叉搜索树的最小绝对差
- [代码随想录](https://programmercarl.com/0530.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E7%9A%84%E6%9C%80%E5%B0%8F%E7%BB%9D%E5%AF%B9%E5%B7%AE.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [530. 二叉搜索树的最小绝对差 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-absolute-difference-in-bst/description/)
- 题意，找二叉搜索树中任意两个节点两值之差的最小值
- 方法，由于二叉搜索树的本质是有序数组，按中序遍历比较相邻节点之差
- 陆爻齐的解法 与代码随想录思路相近
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
      TreeNode *pre = nullptr;
      int getMinimumDifference(TreeNode* root) {
          int result = INT32_MAX;
          travel_cal(result, root);
          return result;
      }
  
      void travel_cal(int &result, TreeNode *root) {
          if (root == nullptr) return;
  
          // 下面是中序
          travel_cal(result, root->left);
          if (pre != nullptr && (root->val - pre->val) < result) {
              result = (root->val - pre->val);
          }
          pre = root;
          travel_cal(result, root->right);
      }
  };
  ```
- ## 二叉搜索树的众数
- [代码随想录](https://programmercarl.com/0501.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%AD%E7%9A%84%E4%BC%97%E6%95%B0.html#%E6%80%9D%E8%B7%AF)
- [501. 二叉搜索树中的众数 - 力扣（LeetCode）](https://leetcode.cn/problems/find-mode-in-binary-search-tree/)
- 题意就是找二叉搜索树的众数，但可能有多个众数
- 方法，寻常方法便是遍历，hash 记录频次，转为 vector 排序取众数，时间复杂度 O(n^2)；但由于是二叉搜索树，可以用 O(n) 的方法，即中序遍历时就直接记录频次和当前最高频次的数字，更新频次时清空已记录的数字
- 陆爻齐参考代码随想录的解法
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
      // 准备
      vector<int> result;
      int max_count = INT32_MIN;
      int count = 1;
      TreeNode *pre = nullptr;
  
      vector<int> findMode(TreeNode* root) {
          travel_val(root);
          return result;
      }
  
      void travel_val(TreeNode *root) {
          if (root == nullptr) return;
  
          // 总体依旧按中序遍历
          travel_val(root->left);
          if (pre != nullptr) { // 更新频次计数
              if (pre->val == root->val) {
                  count++;
              }
              else {
                  count = 1;
              }
          }
          pre = root; // 更新上一个点
          if (count == max_count) {
              result.push_back(root->val);
          }
          else if (count > max_count) {
              max_count = count;
              result.clear();
              result.push_back(root->val);
          }
          travel_val(root->right);
      }
  };
  ```
- ## 二叉树的最近公共祖先
- [代码随想录](https://programmercarl.com/0236.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88.html#%E6%80%9D%E8%B7%AF)
- [236. 二叉树的最近公共祖先 - 力扣（LeetCode）](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/description/)
- 题意，二叉树中无 val 相同点，给两个 val 不同点，找到它们的公共祖先
- 方法，要找公共祖先，最好能自底向上遍历，最类似的遍历是后序遍历，让找到的两个 val 和公共祖先返回上去
- 陆爻齐参考代码随想录思路的解法
  ```
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  class Solution {
  public:
      TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
          // 终止条件，使得上面的点可知道是否子树有 p q
          if (root == nullptr || root->val == p->val || root->val == q->val) return root;
          
          // 遍历左右子树
          TreeNode *left = lowestCommonAncestor(root->left, p, q);
          TreeNode *right = lowestCommonAncestor(root->right, p, q);
  
          // 若有 q 或 p，则返回
          if (left == nullptr || right == nullptr) {
              if (left != nullptr) return left;
              else return right;
          }
          else { // 若 q p都有了，那就返回公共祖先
              return root;
          }
      }
  };
  ```
- ## 二叉搜索树的最近公共祖先
- [代码随想录](https://programmercarl.com/0235.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E7%9A%84%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88.html#%E6%80%9D%E8%B7%AF)
- [235. 二叉搜索树的最近公共祖先 - 力扣（LeetCode）](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)
- 题意，与[[二叉树的最近公共祖先]]类似，不过环岛路二叉搜索树上
- 方法，[[二叉树的最近公共祖先]]的解法也能用，不过二叉搜索树的有序特性能使得代码更简单。由于二叉搜索树中的节点值大于左子树所有节点并小于右子树所有节点，所以两节点的公共祖先的左子树有较小点而右子树有较大点。而从上往下，第一个值在 q 和 p之间的节点就是最近的祖先节点，暂时不知如何证明，姑且作为特性
- 陆爻齐参照代码随想录思路的解法
  ```
  /**
   * Definition for a binary tree node.
   * struct TreeNode {
   *     int val;
   *     TreeNode *left;
   *     TreeNode *right;
   *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
   * };
   */
  
  class Solution {
  public:
      TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
          if (p->val < q->val) swap(p, q); // 确保 p 总是更大的那个
          return findAncestor(root, p, q);
      }
  
      TreeNode *findAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
          // 终止条件，防止空，虽然本题无太大必要
          if (root == nullptr) return root;
  
          if (root->val < q->val) {
              return findAncestor(root->right, p, q);
          }
          else if(root->val > p->val) {
              return findAncestor(root->left, p, q);
          }
          else {
              return root;
          }
      }
  };
  ```
- ## 二叉搜索树中的插入操作
- [代码随想录](https://programmercarl.com/0701.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%AD%E7%9A%84%E6%8F%92%E5%85%A5%E6%93%8D%E4%BD%9C.html#%E5%85%B6%E4%BB%96%E8%AF%AD%E8%A8%80%E7%89%88%E6%9C%AC)
- [701. 二叉搜索树中的插入操作 - 力扣（LeetCode）](https://leetcode.cn/problems/insert-into-a-binary-search-tree/description/)
- 题意，在二叉搜索树中插入点，并返回插入后的树
- 方法，在[[二叉搜索树的搜索]]后的结束处插入即可，这是最简单朴实直观的办法，无需重构二叉搜索树
- 陆爻齐参考代码随想录思路的解法
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
      TreeNode* insertIntoBST(TreeNode* root, int val) {
          // 终止条件，找到插入地方
          if (root == nullptr) {
              TreeNode *result = new TreeNode(val);
              return result;
          }
  
          // 不会有相同值点的插入寻找
          if (val > root->val) {
              root->right = insertIntoBST(root->right, val);
          }
          else {
              root->left = insertIntoBST(root->left, val);
          }
          return root;
      }
  };
  ```
- ## 删除二叉搜索树中的节点
- [代码随想录](https://programmercarl.com/0450.%E5%88%A0%E9%99%A4%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%AD%E7%9A%84%E8%8A%82%E7%82%B9.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [450. 删除二叉搜索树中的节点 - 力扣（LeetCode）](https://leetcode.cn/problems/delete-node-in-a-bst/description/)
- 题意，如题要删点后的树
- 方法，删点有五种情况，1. 没找到，2. 点无子树，3.点只有左子树，4.点只有右子树，5.点左右子树都有。重点是第五种情况，需要把左子树放到右子树的最左节点的左子树，然后用右子树代替
- 陆爻齐参考代码随想录思路的解法
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
      TreeNode* deleteNode(TreeNode* root, int key) {
          if (root == nullptr) return nullptr;
          
          if (root->val != key) { // 没找到
              root->left = deleteNode(root->left, key);
              root->right = deleteNode(root->right, key);
          }
          else if (root->left == nullptr && root->right == nullptr) { // 都为空，直接返空
              delete root;
              return nullptr;
          }
          else if(root->left != nullptr && root->right == nullptr) { // 只有左
              TreeNode *result = root->left;
              delete root;
              return result;
          }
          else if(root->left == nullptr && root->right != nullptr) { // 只有右
              TreeNode *result = root->right;
              delete root;
              return result;
          }
          else { // 左右都有，把左放右的最左，返右
              TreeNode *cur = root->right;
              while(cur->left != nullptr) cur = cur->left;
              cur->left = root->left;
              cur = root->right;
              delete root;
              return cur;
          }
  
          return root;
      }
  };
  ```
- ## 修剪二叉搜索树
- [代码随想录](https://programmercarl.com/0669.%E4%BF%AE%E5%89%AA%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.html)
- [669. 修剪二叉搜索树 - 力扣（LeetCode）](https://leetcode.cn/problems/trim-a-binary-search-tree/submissions/577278029/)
- 题意，给出一个数值范围，要求二叉搜索树内的节点都得在这个范围内
- 方法，直观一点可以转为数组重建二叉搜索树，略复杂；其实可以利用二叉搜索树性质，递归返回符合条件的节点来构建二叉搜索树
- 陆爻齐参考代码随想录思路的解法
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
      TreeNode* trimBST(TreeNode* root, int low, int high) {
          if (root == nullptr) return nullptr; // 一个终止条件
  
          // 由于二叉搜索树有序，当前根过小或过大就可以直接向另一方向搜索
          if (root->val < low) {
              return trimBST(root->right, low, high);
          } 
          else if (root->val > high) {
              return trimBST(root->left, low, high);
          }
  
          // 即使根符合要求，不保证子树所有节点也符合
          root->left = trimBST(root->left, low, high);
          root->right = trimBST(root->right, low, high);
          return root;
      }
  };
  ```
- ## 将有序数组转换为二叉搜索树
- [代码随想录](https://programmercarl.com/0108.%E5%B0%86%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E8%BD%AC%E6%8D%A2%E4%B8%BA%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.html#%E6%80%9D%E8%B7%AF)
- [108. 将有序数组转换为二叉搜索树 - 力扣（LeetCode）](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)
- 题意：将有序的升序数组转化为平衡二叉搜索树
- 方法，平衡，要求点得从中间取，由于升序，故不断取中点递归即可
- 陆爻齐参考代码随想录思路的解法
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
      TreeNode* sortedArrayToBST(vector<int>& nums) {
          return buildTree(nums, 0, nums.size());
      }
  
      TreeNode *buildTree(vector<int>& nums, int left, int right) {
          // 终止条件区间长度为0
          if (left == right) {
              return nullptr;
          }
  
          // 中间，也就是当前的节点
          int middle = (left + right) / 2;
          TreeNode *cur = new TreeNode(nums[middle]);
          cur->left = buildTree(nums, left, middle);
          cur->right = buildTree(nums, middle + 1, right);
          return cur;
      }
  };
  ```
- ## 把二叉搜索树转换为累加树
- [代码随想录](https://programmercarl.com/0108.%E5%B0%86%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E8%BD%AC%E6%8D%A2%E4%B8%BA%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [538. 把二叉搜索树转换为累加树 - 力扣（LeetCode）](https://leetcode.cn/problems/convert-bst-to-greater-tree/)
- 题意，在二叉搜索树上，从右向左，从下向上，把点的值累加上去
- 方法，经观察，点加值的情况符合“右中左”的遍历顺序，故用一个变量记录遍历过的点的累加值，并按右中左来遍历
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
      int pre = 0;
      TreeNode* convertBST(TreeNode* root) {
          if (root == nullptr) return nullptr;
          root->right = convertBST(root->right);
          pre += root->val;
          root->val = pre;
          root->left = convertBST(root->left);
  
          return root;
      }
  };
  ```
# 回溯
- ## 组合
- [代码随想录](https://programmercarl.com/0077.%E7%BB%84%E5%90%88.html#%E6%80%9D%E8%B7%AF)
- [77. 组合 - 力扣（LeetCode）](https://leetcode.cn/problems/combinations/description/)
- 题意，在 1-n 个数中，取 k 个数的所有组合
- 方法，采用普通的回溯方法，注意优化方法，由于限制要 k 个，所以如果一层的 for 循环最多只要遍历到 n-(k-层数)+1 即可，加一是因为自己要求了左闭区间
- 陆爻齐参考代码随想录思路的剪枝代码
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
      void backtrack(int n, int k, int start_index) {
          // 终止条件保存结果
          if (k == path.size()) {
              result.push_back(path);
              return;
          }
  
          // 寻找组合
          for (int i = start_index; i <= (n - (k - path.size()) + 1); i++) {
              path.push_back(i);
              backtrack(n, k, i+1);
              path.pop_back();
          }
      }
  public:
      
      vector<vector<int>> combine(int n, int k) {
          // 两个clear是为了保险
          result.clear();
          path.clear();
          backtrack(n, k , 1);
          return result;
          
      }
  };
  ```
- ## 组合总和 |||
- [代码随想录](https://programmercarl.com/0216.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8CIII.html#%E6%80%9D%E8%B7%AF)
- [216. 组合总和 III - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum-iii/description/)
- 该题要求，收集所有从1-9中选取 k 个不重复的数字组成和为 n 的组合
- 方法，就是在[[组合]]上简单改造终止条件为和为n，剪枝可从和入手，过大就剪
- 陆爻齐的剪枝思路，和代码随想录思路差不多
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
      int sum = 0;
      void backTrack(int k, int n, int start_index) {
          if(sum > n) { // 剪枝
              return;
          }
  
          if (path.size() == k) { // 终止条件
              if(sum == n) result.push_back(path);
              return;
          }
  
          // 单层处理
          for (int i = start_index; i < 10; i++) {
              path.push_back(i);
              sum += i;
              backTrack(k, n, i+1);
              path.pop_back();
              sum -= i;
          }
      }
  public:
      vector<vector<int>> combinationSum3(int k, int n) {
          result.clear();
          path.clear();
          backTrack(k, n, 1);
          return result;
      }
  };
  ```
- ## 电话号码的字母组合
- [代码随想录](https://programmercarl.com/0017.%E7%94%B5%E8%AF%9D%E5%8F%B7%E7%A0%81%E7%9A%84%E5%AD%97%E6%AF%8D%E7%BB%84%E5%90%88.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [17. 电话号码的字母组合 - 力扣（LeetCode）](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/description/)
- 题意，电话号码中的2-9各对应几个字母，要求根据一串2-9的数字来排列出所有字母组合
- 方法，在[[组合]]的基础上，该题有多个字母区间，可先用数组存下下标数字与字符串的对应，再做单层遍历
- 陆爻齐参照代码随想录思路的解法
  ```
  class Solution {
  private:
      vector<string> result;
      string path;
      vector<string> str_map = {
          "",
          "",
          "abc",
          "def",
          "ghi",
          "jkl",
          "mno",
          "pqrs",
          "tuv",
          "wxyz"
      };
  
      void back_track(const string &digits, int start_index) {
          if (start_index == digits.size()) {
              result.push_back(path);
              return;
          }
  
          string range = str_map[int(digits[start_index] - '0')];
          for (auto c : range) {
              path += c;
              back_track(digits, start_index+1);
              path = path.substr(0, path.size()-1);
          }
          
      }
  public:
      vector<string> letterCombinations(string digits) {
          result.clear();
          path.clear();
          if (digits.size() == 0) return result;
          back_track(digits, 0);
          return result;
      }
  };
  ```
- ## 组合总和
- [代码随想录](https://programmercarl.com/0039.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
- [39. 组合总和 - 力扣（LeetCode）](https://leetcode.cn/problems/combination-sum/description/)
- 题意，从无重复元素且数字大于零的整形数组中可重复选取元素，选出所有和为 target 的组合
- 方法，由于可重复选取，故在递归下一层时，不用令选值区间左侧+1来避免重复，且可通过排序然后剪去和大于目标的部分剪枝
- 陆爻齐参考代码随想录思路的方法
  ```
  class Solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
  
      void back_track(vector<int> &candidates, int target, int sum, int start_index) {
          // 终止条件
          if (sum > target) {
              return;
          }
  
          if (sum == target) result.push_back(path);
          // 排序后，可根据和大于目标来剪枝
          for (int i = start_index; i < candidates.size() && sum + candidates[i] <= target; i++) {
              path.push_back(candidates[i]);
              sum += candidates[i];
              back_track(candidates, target, sum, i);
              sum -= candidates[i];
              path.pop_back();
          }
  
  
      }
  public:
      vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
          result.clear();
          path.clear();
          sort(candidates.begin(), candidates.end()); // 便于剪枝的排序
          back_track(candidates, target, 0, 0);
          return result;
      }
  };
  ```