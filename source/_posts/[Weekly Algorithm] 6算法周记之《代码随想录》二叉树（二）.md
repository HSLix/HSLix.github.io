---
title: 【Weekly Algorithm】算法周记之《代码随想录》二叉树（二）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: '41847677'
date: 2024-10-20 22:12:00
---
# 学习小结
本周跟随《代码随想录》学习二叉树的层序遍历，以及对于二叉树的翻转、对称，深度问题等。

# 二叉树
- ## 二叉树的层序遍历
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0102.%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E5%B1%82%E5%BA%8F%E9%81%8D%E5%8E%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- [102. 二叉树的层序遍历 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-level-order-traversal/description/)
- 题意就是一层层地从左到右遍历二叉树
- 方法就是利用队列，把一层层排进去，一层层遍历，陆爻齐用两个队列实现这个操作，不过通过 for 循环，其实只要一个队列就行
-
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
      vector<vector<int>> levelOrder(TreeNode* root) {
          vector<vector<int>> result;
  
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  rec_val.push_back(cur->val); // 记录当前层遍历的数值
              }
              result.push_back(rec_val); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return result;
      }
  };
  ```
  代码随想录的解法  
  ```
  class Solution {
  public:
      vector<vector<int>> levelOrder(TreeNode* root) {
          queue<TreeNode*> que;
          if (root != NULL) que.push(root);
          vector<vector<int>> result;
          while (!que.empty()) {
              int size = que.size();
              vector<int> vec;
              // 这里一定要使用固定大小size，不要使用que.size()，因为que.size是不断变化的
              for (int i = 0; i < size; i++) {
                  TreeNode* node = que.front();
                  que.pop();
                  vec.push_back(node->val);
                  if (node->left) que.push(node->left);
                  if (node->right) que.push(node->right);
              }
              result.push_back(vec);
          }
          return result;
      }
  };
  ```
-
- ##
- ## 二叉树的层次遍历 II
- [107. 二叉树的层序遍历 II - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)
- 题意，在层序遍历的基础上，要求从下向上遍历
- 方法，在层序遍历后，把结果翻转下就可以了
- 陆爻齐的解法（和代码随想录差不多）
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
      vector<vector<int>> levelOrderBottom(TreeNode* root) {
          vector<vector<int>> result;
  
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  rec_val.push_back(cur->val); // 记录当前层遍历的数值
              }
              result.push_back(rec_val); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
          reverse(result.begin(),result.end());
          return result;
      }
  };
  ```
-
- ## 二叉树的右视图
- [199. 二叉树的右视图 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-right-side-view/)
- 题意，要求输出一个二叉树右边看到的第一层数值，从上往下
- 方法，层序遍历时，只保留最后一个值即可
- 陆爻齐的解法（代码随想录也差不多）
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
      vector<int> rightSideView(TreeNode* root) {
          vector<int> result;
  
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  rec_val.push_back(cur->val); // 记录当前层遍历的数值
              }
              result.push_back(rec_val.back()); // 记录一层的最后一个
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return result;
      }
  };
  ```
-
- ## 二叉树的层平均值
- [637. 二叉树的层平均值 - 力扣（LeetCode）](https://leetcode.cn/problems/average-of-levels-in-binary-tree/description/)
- 题意，即求每一层的均值
- 方法，层序遍历基础上，记录每层均值保存即可
- 陆爻齐解法（和代码随想录差不多）
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
      vector<double> averageOfLevels(TreeNode* root) {
          vector<double> result;
  
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              double sum = 0;
              double count = 0;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  //rec_val.push_back(cur->val); // 记录当前层遍历的数值
                  sum += cur->val;
                  count++;
              }
              result.push_back(sum / count); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return result;
      }
  };
  ```
-
- ## N 叉树的层序遍历
- 题意，如题
- 方法，把普通二叉树的层序遍历，中下一层的添加，换成遍历其孩子就行
- 陆爻齐的解法（和代码随想录相近）
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
      vector<vector<int>> levelOrder(Node* root) {
          vector<vector<int>> result;
  
          queue<Node*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<Node*> rec_nextlevel;
              // 遍历一层
              while(!rec.empty()) {
                  Node *cur = rec.front();
                  rec.pop();
                  // 存储下一层
                  for (Node *tmp : cur->children) {
                      if (tmp != nullptr) {
                          rec_nextlevel.push(tmp);
                      }
                  }
  
                  rec_val.push_back(cur->val); // 记录当前层遍历的数值
              }
              result.push_back(rec_val); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return result;
      }
  };
  ```
-
- ##
- ## 在每个树行中找最大值
- [515. 在每个树行中找最大值 - 力扣（LeetCode）](https://leetcode.cn/problems/find-largest-value-in-each-tree-row/submissions/573871357/)
- 题意，找出二叉树中每层的最大值
- 方法，就是在层序遍历的基础上，去记录每层的最大值
- 陆爻齐的解法（与代码随想录差不多）
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
      vector<int> largestValues(TreeNode* root) {
          vector<int> result;
  
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              int max_num = INT32_MIN;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  //rec_val.push_back(cur->val); // 记录当前层遍历的数值
                  if (cur->val > max_num) {
                      max_num = cur->val;
                  }
              }
              result.push_back(max_num); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return result;
      }
  };
  ```
-
- ##
- ##
- ## 填充每个节点的下一个右侧节点指针
- [116. 填充每个节点的下一个右侧节点指针 - 力扣（LeetCode）](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)
- 题意，让二叉树的每个节点都指向其右侧节点，无则指空
- 方法，在层序遍历中，取点让其指右即可，代码随想录中用 for 循环看着挺复杂的样子，其实只是他专门区分了第一个点和其它点
- 陆爻齐的解法（这次和代码随想录有点差别）
  ```
  /*
  // Definition for a Node.
  class Node {
  public:
      int val;
      Node* left;
      Node* right;
      Node* next;
  
      Node() : val(0), left(NULL), right(NULL), next(NULL) {}
  
      Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}
  
      Node(int _val, Node* _left, Node* _right, Node* _next)
          : val(_val), left(_left), right(_right), next(_next) {}
  };
  */
  
  class Solution {
  public:
      Node* connect(Node* root) {
          //vector<vector<int>> result;
  
          queue<Node*> rec; // 记录当前要遍历的层
          if (root != nullptr) rec.push(root);
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<Node*> rec_nextlevel;
              // 遍历一层
              while(!rec.empty()) {
                  Node *cur = rec.front();
                  rec.pop();
  
                  
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
                  rec_val.push_back(cur->val); // 记录当前层遍历的数值
                  
                  // 让每个节点指右边（无则指空）
                  if (!rec.empty()) {
                      cur->next = rec.front();
                  }
                  else {
                      cur->next = nullptr;
                  }
              }
              //result.push_back(rec_val); // 记录一层
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return root;
      }
  };
  ```
  代码随想录的解法  
  ```
  class Solution {
  public:
      Node* connect(Node* root) {
          queue<Node*> que;
          if (root != NULL) que.push(root);
          while (!que.empty()) {
              int size = que.size();
              // vector<int> vec;
              Node* nodePre;
              Node* node;
              for (int i = 0; i < size; i++) {
                  if (i == 0) {
                      nodePre = que.front(); // 取出一层的头结点
                      que.pop();
                      node = nodePre;
                  } else {
                      node = que.front();
                      que.pop();
                      nodePre->next = node; // 本层前一个节点next指向本节点
                      nodePre = nodePre->next;
                  }
                  if (node->left) que.push(node->left);
                  if (node->right) que.push(node->right);
              }
              nodePre->next = NULL; // 本层最后一个节点指向NULL
          }
          return root;
  
      }
  };
  ```
-
- ##
- ## 填充每个节点的下一个右侧节点指针II
- 题意，和上一题差不多，不过面对的二叉树不是完美二叉树
- 方法，和上一题一样，不知此题有何意义
- 解法略，同上
-
- ##
- ## 二叉树的最大深度
- 题意，即找出二叉树的根节点与其最远叶节点的距离
- 方法，层序遍历时，记录层数即可
- 陆爻齐的解法（和代码随想录差不多）
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
          int depth = 0;
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) {
              rec.push(root);
          }
          
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              depth++;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  rec.pop();
  
                  if (cur->left != nullptr) rec_nextlevel.push(cur->left); // 存储下一层
                  if (cur->right != nullptr) rec_nextlevel.push(cur->right);
  
              }
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return depth;
      }
  };
  ```
-
- ##
- ## 二叉树的最小深度
- [111. 二叉树的最小深度 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)
- 题目要求，找到最小深度，即第一个叶子节点出现的层数
- 方法，每个点检测下，若是叶子节点，则返回当前层数，代码随想录的解法相对陆爻齐的解法重复检测孩子是否为空，陆爻齐 win ：）
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
      int minDepth(TreeNode* root) {
          int depth = 0;
          queue<TreeNode*> rec; // 记录当前要遍历的层
          if (root != nullptr) {
              rec.push(root);
          }
          
  
          while(!rec.empty()) {
              vector<int> rec_val;
              queue<TreeNode*> rec_nextlevel;
              depth++;
              // 遍历一层
              while(!rec.empty()) {
                  TreeNode *cur = rec.front();
                  rec.pop();
                  int count = 0; // 记录有无孩子
  
                  // 存储下一层
                  if (cur->left != nullptr) {
                      rec_nextlevel.push(cur->left); 
                      count++;
                  }
                  if (cur->right != nullptr) {
                      rec_nextlevel.push(cur->right);
                      count++;
                  }
  
                  // 找到了第一个叶子节点
                  if (count == 0) {
                      return depth;
                  }
  
              }
              rec = rec_nextlevel; // 准备遍历下一层
          }
  
          return depth;
      }
  };
  ```
  代码随想录的解法  
  ```
  class Solution {
  public:
      int minDepth(TreeNode* root) {
          if (root == NULL) return 0;
          int depth = 0;
          queue<TreeNode*> que;
          que.push(root);
          while(!que.empty()) {
              int size = que.size();
              depth++; // 记录最小深度
              for (int i = 0; i < size; i++) {
                  TreeNode* node = que.front();
                  que.pop();
                  if (node->left) que.push(node->left);
                  if (node->right) que.push(node->right);
                  if (!node->left && !node->right) { // 当左右孩子都为空的时候，说明是最低点的一层了，退出
                      return depth;
                  }
              }
          }
          return depth;
      }
  };
  ```
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