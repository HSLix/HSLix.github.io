---
title: 【Weekly Algorithm】算法周记之《代码随想录》二叉树（二）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-10-20 22:12:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习二叉树的层序遍历，以及对于二叉树的翻转、对称，深度问题等。

# 二叉树
## 翻转二叉树
- [代码随想录](https://programmercarl.com/0226.%E7%BF%BB%E8%BD%AC%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E6%8B%93%E5%B1%95)
- [226. 翻转二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/invert-binary-tree/description/)
- 题意，把整个二叉树左右翻转
- 方法，其实把每个节点的左右子树交换即可
- 陆爻齐的方法（类似前序）
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
      TreeNode* invertTree(TreeNode* root) {
          // 终止条件
          if (root == nullptr) return root;
  
          // 交换左右子树
          TreeNode *tmp = root->left;
          root->left = root->right;
          root->right = tmp;
          // 递归翻转
          if (root->left != nullptr) invertTree(root->left);
          if (root->right != nullptr) invertTree(root->right);
          return root;
      }
  };
  ```
  代码随想录补充的类中序方法  
  ```
  class Solution {
  public:
      TreeNode* invertTree(TreeNode* root) {
          if (root == NULL) return root;
          invertTree(root->left);         // 左
          swap(root->left, root->right);  // 中
          invertTree(root->left);         // 注意 这里依然要遍历左孩子，因为中间节点已经翻转了
          return root;
      }
  };
  ```
## 对称二叉树
- [代码随想录](https://programmercarl.com/0101.%E5%AF%B9%E7%A7%B0%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E6%80%9D%E8%B7%AF)
- [101. 对称二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/symmetric-tree/)
- 题意，判断二叉树是否沿中间水平对称
- 方法，注意不是判断一个节点的左右子树是否相等，而是判断左子树的右子树与右子树的左子树以及左子树的左子树和右子树的右子树是否相等。
- 陆爻齐参照代码随想录的解法
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
      bool isSymmetric(TreeNode* root) {
          if (root == nullptr) return true;
          return compare(root->left, root->right);
      }
  
      bool compare(TreeNode *left, TreeNode *right) {
          // 判断两节点是否空的相同情况或值不同的情况
          if (left == nullptr && right == nullptr) return true;
          else if (left != nullptr && right == nullptr) return false;
          else if (left == nullptr && right != nullptr) return false;
          else if (left->val != right->val) return false;
  
          // 剩下已是两个节点存在且数值相同，因此要判断他们子树情况
          bool inSide = compare(left->right, right->left);
          bool outSide = compare(left->left, right->right);
          bool isSame = inSide && outSide;
          return isSame;
  
      }
  };
  ```


## 浅谈二叉树的深度和高度
- [代码随想录](https://programmercarl.com/0104.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%9C%80%E5%A4%A7%E6%B7%B1%E5%BA%A6.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 二叉树的深度指根节点到叶子节点的最大距离，计算时相当于从上往下，1、2、3……这么数；
  
  二叉树的高度，指一个节点到叶子节点的最大距离，计算时相当于从下往上数  
- 易得，深度适合用前序遍历，而高度适合用后序遍历
- 根节点的高度就是二叉树的深度
## 二叉树的最大深度
- [104. 二叉树的最大深度 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-depth-of-binary-tree/description/)
- 题意，即求根节点到叶节点的最长距离
- 方法，此题在[[二叉树的层序遍历]]有所描述，用层序遍历即可解决，但这里再介绍下递归方法
- 陆爻齐参照代码随想录的解法
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
      int maxDepth(TreeNode* root) {
          if (root == nullptr) return 0;
          int left_depth = maxDepth(root->left);
          int right_depth = maxDepth(root->right);
          int max_depth = max(left_depth,right_depth);
          return 1+max_depth;
  
      }
  };
  ```

## N 叉树的最大深度
- 题意，求 N 叉树的最大深度
- 方法，和上题类似，只要把遍历孩子时用循环遍历完即可
- 陆爻齐的解法（和代码随想录类似）
  ```
  /*
  // Definition for a Node.
  class Node {
  public:
      int val;
      vector<Node*> children;
  
      Node() {}
  
      Node(int _val) {
          val = _val;
      }
  
      Node(int _val, vector<Node*> _children) {
          val = _val;
          children = _children;
      }
  };
  */
  
  class Solution {
  public:
      int max_depth = 0;
      int maxDepth(Node* root) {
          search_depth(root, 0);
          return max_depth;
      }
  
  
      void search_depth(Node *root, int depth) {
          if (root == nullptr) return;
          depth++; // 当前层非零，深度加一
  
          // 遍历孩子，算 DFS
          for (Node *child : root->children) {
              search_depth(child, depth);
          }
          // 更新层数
          if (depth > max_depth) {
              max_depth = depth;
          }
  
      }
  };
  ```

## 二叉树的最小深度
- [代码随想录](https://programmercarl.com/0111.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E6%9C%80%E5%B0%8F%E6%B7%B1%E5%BA%A6.html#%E6%80%9D%E8%B7%AF)
- [111. 二叉树的最小深度 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-depth-of-binary-tree/description/)
- 题意：求二叉树的最小深度，最小深度就是根节点到叶子节点（无左子树和右子树的节点）
- 方法：也可用层序遍历解决，[[二叉树的层序遍历]]，这里也要介绍下递归方法，与[[二叉树的最大深度]]有些许差别，在于对待非叶子节点的方式需要单独设置，尤其是有一侧空的节点，只要另一侧的深度即可
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
      int minDepth(TreeNode* root) {
          return getDepth(root);
      }
  
      int getDepth(TreeNode *root) {
          if (root == nullptr) return 0;
          int left_depth = getDepth(root->left);
          int right_depth = getDepth(root->right);
  
          // 对于有一个点位空的点，不算叶节点，故只传另一侧的深度
          if (left_depth == 0 && right_depth > 0) return 1 + right_depth;
          if(left_depth > 0 && right_depth == 0) return 1 + left_depth;
  
          int min_depth = min(left_depth, right_depth);
          return 1 + min_depth; // 加上本层的点（+1）
      }
  };
  ```