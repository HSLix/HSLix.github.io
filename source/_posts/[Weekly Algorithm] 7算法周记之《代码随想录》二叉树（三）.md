---
title: 【Weekly Algorithm】算法周记之《代码随想录》二叉树（三）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-10-27 21:11:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习二叉树和二叉搜索树的算法题目，比如完全二叉树的节点个数，平衡二叉树，二叉树的路径、二叉树的合并，二叉搜索树的检索和验证等。

# 二叉树
- ## 完全二叉树的节点个数
- [代码随想录](https://programmercarl.com/0222.%E5%AE%8C%E5%85%A8%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E8%8A%82%E7%82%B9%E4%B8%AA%E6%95%B0.html#%E6%80%9D%E8%B7%AF)
- [222. 完全二叉树的节点个数 - 力扣（LeetCode）](https://leetcode.cn/problems/count-complete-tree-nodes/description/)
- 题意就是让你求一个完全二叉树的节点个数
- 方法有大致两种，一种是把这个当普通二叉树求，直接先序、中序、后序遍历都行，统计节点，时间复杂度 O(n)；第二种是利用完全二叉树的特性，由于完全二叉树可视作是满二叉树组成的二叉树，满二叉树的节点由深度 n，经 2^n - 1 计算得出。
- 陆爻齐的先序求法
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
      int result = 0;
      int countNodes(TreeNode* root) {
          pre_val(root);
          return result;
      }
  
      void pre_val(TreeNode *root) {
          if (root == nullptr) return;
  
          result++;
          pre_val(root->left);
          pre_val(root->right);
      }
  };
  ```
  陆爻齐参照代码随想录思路写的利用完全二叉树特性解法  
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
  
      int countNodes(TreeNode* root) {
          int result = full_val(root);
          return result;
      }
  
      int full_val(TreeNode *root) {
          // 空指针的情况
          if (root == nullptr) return 0;
  
          // 满二叉树的情况
          int left_depth = 0, right_depth = 0;
          TreeNode *left = root, *right = root;
          while (left) {
              left = left->left;
              left_depth++;
          }
          while (right) {
              right = right->right;
              right_depth;
          }
          if (left_depth == right_depth) { // 左边深度与右边相等是满二叉树
              return (2 << left_depth) - 1; // 节点数量为 2 的深度次方 - 1
          }
  
          // 非满二叉树，可递归下去
          int left_nodes = full_val(root->left);
          int right_nodes = full_val(root->right);
          return left_nodes + right_nodes + 1;
      }
  };
  ```
- ## 平衡二叉树
- [代码随想录](https://programmercarl.com/0110.%E5%B9%B3%E8%A1%A1%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E6%9C%AC%E9%A2%98%E6%80%9D%E8%B7%AF)
- [110. 平衡二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/balanced-binary-tree/description/)
- 题意，判断该二叉树是否为平衡二叉树，即左右子树高度差小于等于一，高度为节点到叶子节点的最远距离
- 方法就是利用递归求两侧高度，若有非平衡二叉树情况，则使得整棵树不是即可
- 陆爻齐参照代码随想录思路解法
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
      bool isBalanced(TreeNode* root) {
          return getHeight(root) == -1 ? false : true;
      }
  
      int getHeight(TreeNode *root) {
          // 叶子节点高度为 0
          if (root == nullptr) {
              return 0;
          }
          
          // 左右子树若有非平衡二叉树，则该二叉树非平衡二叉树
          int left_height = getHeight(root->left);
          if (left_height == -1) return -1;
          int right_height = getHeight(root->right);
          if (right_height == -1) return -1;
  
          return abs(left_height - right_height) > 1 ? -1 : max(left_height, right_height) + 1;
      }
  };
  ```
- ## 二叉树的所有路径
- [代码随想录](https://programmercarl.com/0257.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%89%80%E6%9C%89%E8%B7%AF%E5%BE%84.html#%E6%80%9D%E8%B7%AF)
- [257. 二叉树的所有路径 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-paths/description/)
- 题意，找到二叉树从根节点到叶子节点的所有路径
- 方法，回溯递归，最重要的点在于，传的 string path 最好不要引用传递，因为每个节点放进去的数字可能有好几位，传引用需要精确去除数字，比较麻烦，传值的话就不用担心后面加的数字影响到这一层的 path。
- 陆爻齐参照代码随想录修复了 path 引用bug的解法
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
      vector<string> binaryTreePaths(TreeNode* root) {
          string path;
          vector<string> result;
          all_val(root, path, result);
          return result;
      }
  
      // 这个path用复制非常重要，引用很难去除本点值
      void all_val(TreeNode *root, string path, vector<string> &result) {
          // 防第一个点就是空
          if (root == nullptr) return;
          
          // 非空时，加入该点值
          path += to_string(root->val);
          //cout << path << endl;
          path += "->";
          if (root->left != nullptr) { // 左子树
              all_val(root->left, path, result); 
          }
  
          if (root->right != nullptr) { // 右子树
              all_val(root->right, path, result);
          }
  
          path = path.substr(0, path.size() - 2); // 去除-》
          // 加入叶子节点的路径
          if (root->left == nullptr && root->right == nullptr) {
              result.push_back(path);
          }
          
      }
  };
  ```
  代码随想录的解法  
  ```
  class Solution {
  private:
      void traversal(TreeNode* cur, string path, vector<string>& result) {
          path += to_string(cur->val); // 中，中为什么写在这里，因为最后一个节点也要加入到path中
          if (cur->left == NULL && cur->right == NULL) {
              result.push_back(path);
              return;
          }
          if (cur->left) {
              path += "->";
              traversal(cur->left, path, result); // 左
              path.pop_back(); // 回溯 '>'
              path.pop_back(); // 回溯 '-'
          }
          if (cur->right) {
              path += "->";
              traversal(cur->right, path, result); // 右
              path.pop_back(); // 回溯'>'
              path.pop_back(); // 回溯 '-'
          }
      }
  
  public:
      vector<string> binaryTreePaths(TreeNode* root) {
          vector<string> result;
          string path;
          if (root == NULL) return result;
          traversal(root, path, result);
          return result;
  
      }
  };
  
  ```
- ## 左叶子之和
- [代码随想录](https://programmercarl.com/0404.%E5%B7%A6%E5%8F%B6%E5%AD%90%E4%B9%8B%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
- [404. 左叶子之和 - 力扣（LeetCode）](https://leetcode.cn/problems/sum-of-left-leaves/description/)
- 题意，找出二叉树中的所有左叶子节点，求其和
- 方法，不难，重点是左叶子的定义，若 A 点的左节点非空为 B，B 无左节点和右节点，那么 B 为左叶子，由此便可直接遍历找左叶子即可
- 陆爻齐的解法
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
      int sumOfLeftLeaves(TreeNode* root) {
          int result = 0;
          pre_val_pro(root, result);
          return result;
      }
  
      void pre_val_pro(TreeNode *root, int &result) {
          if (root == nullptr) return; // 排除空的场景
  
          // 添加左叶子节点
          if (root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr) {
              result+=root->left->val;
          }
  
          // 遍历左右子树
          pre_val_pro(root->left, result);
          pre_val_pro(root->right, result);
      }
  };
  ```
  代码随想录的解法  
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
      int sumOfLeftLeaves(TreeNode* root) {
          if (root == NULL) return 0;
          if (root->left == NULL && root->right== NULL) return 0; //陆爻齐注释后也可AC，这是加速进程的
  
          int leftValue = sumOfLeftLeaves(root->left);    // 左
          if (root->left && !root->left->left && !root->left->right) { // 左子树就是一个左叶子的情况
              leftValue = root->left->val;
          }
          int rightValue = sumOfLeftLeaves(root->right);  // 右
  
          int sum = leftValue + rightValue;               // 中
          return sum;
      }
  };
  
  ```
- ## 找树左下角的值
- [代码随想录](https://programmercarl.com/0513.%E6%89%BE%E6%A0%91%E5%B7%A6%E4%B8%8B%E8%A7%92%E7%9A%84%E5%80%BC.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [513. 找树左下角的值 - 力扣（LeetCode）](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)
- 题意，找出一棵二叉树最底边最左边的值
- 方法，迭代的方法好说，只要层序遍历取最左边，也就是第一个点就可以；但是递归的方法没那么好理解，其实只要在递归时优先遍历下一层就可以保证：第一个深度最深的点，就是最左边的。
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
      int result = 0;
      int max_depth = INT32_MIN;
      int findBottomLeftValue(TreeNode* root) {
          search_left(root, 0);
          return result;
      }
  
      void search_left(TreeNode *root, int depth) {
          if (root == nullptr) return;
  
          // 由于总是先遍历左边，故第一个大深度必为最左
          if (root->left == nullptr && root->right == nullptr) {
              if (depth > max_depth) {
                  max_depth = depth;
                  result = root->val;
              }
              return;
          }
  
          // 先左后右
          search_left(root->left, depth+1);
          search_left(root->right, depth+1);
  
      }
  };
  ```
- ## 路径总和
- [代码随想录](https://programmercarl.com/0112.%E8%B7%AF%E5%BE%84%E6%80%BB%E5%92%8C.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [112. 路径总和 - 力扣（LeetCode）](https://leetcode.cn/problems/path-sum/)
- 题意，给一个目标和，要求判断二叉树中是否有一条根节点到叶节点的路径和恰好为目标和
- 方法，DFS，递归或迭代
- 陆爻齐的解法，比较冗余，不过不用多改下一题也能用：）
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
      vector<int> path; // 记录路径，主要是记上一层的数字
      bool result = false; // 记录结果
      int sum = 0; // 记录和
      bool hasPathSum(TreeNode* root, int targetSum) {
          val(root, targetSum);
          return result;
      }
  
      void val(TreeNode *root, int targetSum) {
          if (root == nullptr) return; // 空节点的终止条件
          path.push_back(root->val); // 加入路径并更新路径和
          sum += root->val;
          if (root->left == nullptr && root->right == nullptr && sum == targetSum) { // 到了叶节点且和恰好相同才行
              result = true;
              return;
          }
  
          val(root->left, targetSum); 
          val(root->right, targetSum);
  
          sum -= path[path.size()-1]; // 去除路径
          path.erase(path.end()-1); // 去除和
      }
  };
  ```
  代码随想录的解法  
  ```
  class Solution {
  private:
      bool traversal(TreeNode* cur, int count) {
          if (!cur->left && !cur->right && count == 0) return true; // 遇到叶子节点，并且计数为0
          if (!cur->left && !cur->right) return false; // 遇到叶子节点直接返回
  
          if (cur->left) { // 左
              count -= cur->left->val; // 递归，处理节点;
              if (traversal(cur->left, count)) return true;
              count += cur->left->val; // 回溯，撤销处理结果
          }
          if (cur->right) { // 右
              count -= cur->right->val; // 递归，处理节点;
              if (traversal(cur->right, count)) return true;
              count += cur->right->val; // 回溯，撤销处理结果
          }
          return false;
      }
  
  public:
      bool hasPathSum(TreeNode* root, int sum) {
          if (root == NULL) return false;
          return traversal(root, sum - root->val);
      }
  };
  ```
- ## 路径总和ii
- [113. 路径总和 II - 力扣（LeetCode）](https://leetcode.cn/problems/path-sum-ii/submissions/574988363/)
- 题意，基本同上一题，但要求返回所有和为目标和的路径
- 方法，用vector记路径，再递归即可
- 陆爻齐的解法
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
      vector<int> path; // 记录路径，主要是记上一层的数字
      vector<vector<int>> result; // 记录结果
      int sum = 0; // 记录和
  
      void val(TreeNode *root, int targetSum) {
          if (root == nullptr) return; // 空节点的终止条件
          path.push_back(root->val); // 加入路径并更新路径和
          sum += root->val;
          if (root->left == nullptr && root->right == nullptr && sum == targetSum) { // 到了叶节点且和恰好相同才行
              result.push_back(path);
          }
  
          val(root->left, targetSum); 
          val(root->right, targetSum);
  
          sum -= path[path.size()-1]; // 去除路径
          path.erase(path.end()-1); // 去除和
      }
  
      vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
          val(root, targetSum);
          return result;
      }
  };
  ```
  代码随想录的解法  
  ```
  class solution {
  private:
      vector<vector<int>> result;
      vector<int> path;
      // 递归函数不需要返回值，因为我们要遍历整个树
      void traversal(TreeNode* cur, int count) {
          if (!cur->left && !cur->right && count == 0) { // 遇到了叶子节点且找到了和为sum的路径
              result.push_back(path);
              return;
          }
  
          if (!cur->left && !cur->right) return ; // 遇到叶子节点而没有找到合适的边，直接返回
  
          if (cur->left) { // 左 （空节点不遍历）
              path.push_back(cur->left->val);
              count -= cur->left->val;
              traversal(cur->left, count);    // 递归
              count += cur->left->val;        // 回溯
              path.pop_back();                // 回溯
          }
          if (cur->right) { // 右 （空节点不遍历）
              path.push_back(cur->right->val);
              count -= cur->right->val;
              traversal(cur->right, count);   // 递归
              count += cur->right->val;       // 回溯
              path.pop_back();                // 回溯
          }
          return ;
      }
  
  public:
      vector<vector<int>> pathSum(TreeNode* root, int sum) {
          result.clear();
          path.clear();
          if (root == NULL) return result;
          path.push_back(root->val); // 把根节点放进路径
          traversal(root, sum - root->val);
          return result;
      }
  };
  ```
- ## 从中序与后序遍历序列构造二叉树
- [代码随想录](https://programmercarl.com/0106.%E4%BB%8E%E4%B8%AD%E5%BA%8F%E4%B8%8E%E5%90%8E%E5%BA%8F%E9%81%8D%E5%8E%86%E5%BA%8F%E5%88%97%E6%9E%84%E9%80%A0%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [106. 从中序与后序遍历序列构造二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/description/)
- 题意便是根据一颗二叉树的中序和后序来构造二叉树
- 方法，利用后序遍历的最后一个点为根，且中序的根左侧为左子树，右侧为右子树，递归下来可得唯一二叉树
- 陆爻齐参照代码随想录思路的复制 vector 的解法
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
      TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
          if (postorder.size() == 0) return nullptr;
  
          int mid_val = postorder[postorder.size()-1]; // 取当前的中点
          TreeNode *root = new TreeNode(mid_val);
  
          int mid_index = 0; // 找中序里面中点位置
          for (int i = 0; i < inorder.size(); i++) {
              if (inorder[i] == mid_val) {
                  mid_index = i;
                  break;
              }
          }
  
          // 下面切割按左闭右开来算
          // 中序切割顺便去除中点
          vector<int> left_inorder = vector<int>(inorder.begin(), inorder.begin()+mid_index);
          vector<int> right_inorder = vector<int>(inorder.begin()+mid_index+1, inorder.end());
          // 后序要去除最后一个
          vector<int> left_postorder = vector<int>(postorder.begin(), postorder.begin()+mid_index);
          vector<int> right_postorder = vector<int>(postorder.begin()+mid_index, postorder.end()-1);
  
          root->left = buildTree(left_inorder, left_postorder);
          root->right = buildTree(right_inorder, right_postorder);
  
          return root;
      }
  };
  ```
  陆爻齐根据上述思路不复制vector只传坐标的优化办法：  
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
  private:
      vector<int>inorder;
      vector<int>postorder;
  public:
      TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
          this->inorder = inorder;
          this->postorder = postorder;
          TreeNode *root = travel_build(0, inorder.size(), 0, postorder.size());
  
          return root;
      }
  
      TreeNode *travel_build(int inorder_left, int inorder_right, int postorder_left, int postorder_right) {
          if (inorder_right == inorder_left) return nullptr; // 说明该子树无节点
  
          int root_val = this->postorder[postorder_right-1];
          TreeNode *root = new TreeNode(root_val); // 取本子树的根
  
          int left_inorder_count = 0; // 相当于左子树有几个节点
          for (int i = inorder_left; i < inorder_right; i++) {
              if (inorder[i] == root_val) {
                  break;
              }
              left_inorder_count++;
          }
  
          // 中序去中点，后序去末点
          root->left = travel_build(inorder_left, inorder_left+left_inorder_count, postorder_left, postorder_left+left_inorder_count);
          root->right = travel_build(inorder_left+left_inorder_count+1, inorder_right, postorder_left+left_inorder_count, postorder_right-1);
          
          return root;
      }
  };
  ```
- ## 从前序与中序遍历序列构造二叉树
- [105. 从前序与中序遍历序列构造二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/)
- 题意，如题
- 方法，基本同上，只不过找中点是什么值换成了前序而已，前序的中点在第一个
- 陆爻齐在上面方法改出来的解法
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
      vector<int> inorder;
      vector<int> preorder;
      TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
          this->inorder = inorder;
          this->preorder = preorder;
          TreeNode *root = travel_build(0, inorder.size(), 0, preorder.size());
  
          return root;
      }
  
      TreeNode *travel_build(int inorder_left, int inorder_right, int preorder_left, int preorder_right) {
          if (inorder_right == inorder_left) return nullptr; // 说明该子树无节点
          //if (preorder_right <= preorder_left) return nullptr; // 说明该子树无节点
          //cout << "il:" << inorder_left << "; ir:" << inorder_right << "; pl:" << preorder_left << "; pr:" << preorder_right << endl;
          int root_val = this->preorder[preorder_left];
          TreeNode *root = new TreeNode(root_val); // 取本子树的根
          //cout << "root:" << root_val << endl;
          //cout << "il:" << inorder_left << "; ir:" << inorder_right << "; pl:" << preorder_left << "; pr:" << preorder_right << endl;
          int left_inorder_count = 0; // 相当于左子树有几个节点
          for (int i = inorder_left; i < inorder_right; i++) {
              if (inorder[i] == root_val) {
                  break;
              }
              left_inorder_count++;
          }
  
          // 中序去中点，先序去首个点
          root->left = travel_build(inorder_left, inorder_left+left_inorder_count, preorder_left+1, preorder_left+1+left_inorder_count);
          root->right = travel_build(inorder_left+left_inorder_count+1, inorder_right, preorder_left+1+left_inorder_count, preorder_right);
          
          return root;
      }
  };
  ```
- ## 最大二叉树
- [代码随想录](https://programmercarl.com/0654.%E6%9C%80%E5%A4%A7%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [654. 最大二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-binary-tree/description/)
- 题意：像大顶堆一样，二叉树的根为最大值，左子树根为数组中左边的最大值，右子树同理
- 方法，递归取大放根即可，陆爻齐直接写出了下标的简洁解法
- 陆爻齐的解法，与代码随想录的优化解法相近
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
      TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
          // 根据下标构造二叉树，按照左闭右开原则
          TreeNode *result = find_max_build_tree(nums, 0, nums.size());
  
          return result;
      }
  
      TreeNode *find_max_build_tree(vector<int> &nums, int left, int right) {
          if (right <= left) return nullptr; //终止条件，即判断的区间为0
  
          // 从下标范围找最大值以及下标
          int max_num = INT32_MIN;
          int max_index = left;
          for (int i = left; i < right; i++) {
              if (nums[i] > max_num) {
                  max_num = nums[i];
                  max_index = i;
              }
          }
  
          // 构造本节点下标
          TreeNode *result = new TreeNode(max_num);
  
          // 左子树与右子树
          result->left = find_max_build_tree(nums, left, max_index);
          result->right = find_max_build_tree(nums, max_index+1, right);
  
          return result;
  
      }
  };
  ```
- ## 合并二叉树
- [代码随想录](https://programmercarl.com/0617.%E5%90%88%E5%B9%B6%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [617. 合并二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/merge-two-binary-trees/description/)
- 题意，两个二叉树相同位置的节点值相加（int），相当于重合一起
- 方法，递归算重合，陆爻齐写得繁琐，其实一边为空时，可以直接返回非空部分
- 陆爻齐的解法
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
      TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
          TreeNode *result = nullptr;
          if (root1 || root2) { // 两边有一个非空，那么该节点非空
              if (root1 == nullptr) {
                  result = new TreeNode(root2->val);
                  result->left = mergeTrees(root1, root2->left);
                  result->right = mergeTrees(root1, root2->right);
              }
              else if (root2 == nullptr) {
                  result = new TreeNode(root1->val);
                  result->left = mergeTrees(root1->left, root2);
                  result->right = mergeTrees(root1->right, root2);
              }
              else {
                  result = new TreeNode(root1->val + root2->val);
                  result->left = mergeTrees(root1->left, root2->left);
                  result->right = mergeTrees(root1->right, root2->right);
              }
              
          }
          return result;
      }
  };
  ```
  代码随想录解法  
  ```
  class Solution {
  public:
      TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
          if (t1 == NULL) return t2; // 如果t1为空，合并之后就应该是t2
          if (t2 == NULL) return t1; // 如果t2为空，合并之后就应该是t1
          // 修改了t1的数值和结构
          t1->val += t2->val;                             // 中
          t1->left = mergeTrees(t1->left, t2->left);      // 左
          t1->right = mergeTrees(t1->right, t2->right);   // 右
          return t1;
      }
  };
  ```
- ## 二叉搜索树的搜索
- [代码随想录](https://programmercarl.com/0700.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E4%B8%AD%E7%9A%84%E6%90%9C%E7%B4%A2.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [700. 二叉搜索树中的搜索 - 力扣（LeetCode）](https://leetcode.cn/problems/search-in-a-binary-search-tree/description/)
- 题意：在二叉搜索树上找对应值的节点
- 方法，重点是认识二叉搜索树的概念，二叉搜索树的根左子树小于根，右子树大于根，且子树皆满足这个性质，所以搜索很简单，大了就向右，小了向左
- 陆爻齐的递归方法
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
      TreeNode* searchBST(TreeNode* root, int val) {
          if (root == nullptr) return nullptr; // 说明没有这个val 的节点
  
          if (root->val == val) return root;// 找到了
          else if (root->val < val) return searchBST(root->right, val); // val偏大
          else return searchBST(root->left, val); // val偏小
      }
  };
  ```
  陆爻齐的迭代方法  
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
      TreeNode* searchBST(TreeNode* root, int val) {
          while (root != nullptr) {
              if (root->val == val) break;
              else if (root->val > val) root = root->left;
              else root = root->right;
          }
          return root;
      }
  };
  ```
- ## 验证二叉搜索树
- [代码随想录](https://programmercarl.com/0098.%E9%AA%8C%E8%AF%81%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91.html#%E6%80%9D%E8%B7%AF)
- [98. 验证二叉搜索树 - 力扣（LeetCode）](https://leetcode.cn/problems/validate-binary-search-tree/description/)
- 题意，验证一个二叉树是否为二叉搜索树
- 方法，递归查
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
      TreeNode *pre = nullptr; // 记录上一个访问节点，由于中序，所以会按二叉搜索左到右
      bool travelTree(TreeNode *root) {
          if (root == nullptr) return true; // 空树也算二叉搜索树
  
          bool left = travelTree(root->left); // 中序
          if (pre != nullptr && pre->val >= root->val) return false;
          pre = root;
          bool right = travelTree(root->right);
  
          return left && right;
      }
  public:
      bool isValidBST(TreeNode* root) {
          return travelTree(root);
      }
  };
  ```