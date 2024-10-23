---
title: 【Weekly Algorithm】算法周记之《代码随想录》栈与队列与二叉树（一）
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-10-13 19:48:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习栈与队列和二叉树遍历的一部分，学到了栈和队列在算法中的应用，以及优先级队列的使用；此外还有二叉树的前中后序的递归、迭代、统一迭代方法。

# 栈与队列

## 用栈实现队列
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0232.%E7%94%A8%E6%A0%88%E5%AE%9E%E7%8E%B0%E9%98%9F%E5%88%97.html#%E6%80%9D%E8%B7%AF)
- 题意就是用栈实现队列的一部分功能
- 核心就是通过两栈，一个管输入，一个管输出，输出空了时，执行输出操作需从输入中把数字倒入输出的栈中
- 陆爻齐参考代码随想录思路的解法
  ```
  class MyQueue {
  public:
      stack<int> in;
      stack<int> out;
  
      MyQueue() {
          
      }
      
      void push(int x) {
          in.push(x);
      }
      
      int pop() {
          int num = this->peek();
          out.pop();
          return num;
      }
      
      int peek() {
          // 这个判空很重要，意味着如果在两次push中有pop，下次pop不会得到刚push进去的数字，而是按队列下去
          if (out.empty()) {
              while(!in.empty()) {
              out.push(in.top());
              in.pop();
              }
          }
          
          return out.top();
      }
      
      bool empty() {
          return in.empty() && out.empty();
      }
  };
  
  /**
   * Your MyQueue object will be instantiated and called as such:
   * MyQueue* obj = new MyQueue();
   * obj->push(x);
   * int param_2 = obj->pop();
   * int param_3 = obj->peek();
   * bool param_4 = obj->empty();
   */
  ```

## 用队列实现栈
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0225.%E7%94%A8%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E6%A0%88.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 如题，用队列实现栈，强调是单向队列
- 乍一看，还想用 [[用栈实现队列]] 的方法，两个队列，一个模拟输入，一个模拟输出，但其实不行，因为队列先进先出，不会改变输入顺序，另一个队列只能起到备份作用，甚至乎，只要一个队列就行，在 pop（）时先获取队列长度 size，把前面 size-1 个元素放后面，再把最前面拿出来就行。
- 陆爻齐参考代码随想录思路的解法
  ```
  class MyStack {
  public:
      queue<int> q;
      queue<int> backup;
      MyStack() {
  
      }
      
      void push(int x) {
          q.push(x);
      }
      
      int pop() {
      	// 备份
          while(q.size() > 1) {
              backup.push(q.front());
              q.pop();
          }
          int top = q.front();
          q.pop();
          // 恢复
          while (!backup.empty()) {
              q.push(backup.front());
              backup.pop();
          }
          return top;
      }
      
      int top() {
          int top = this->pop();
          this->push(top);
          return top;
      }
      
      bool empty() {
          return q.empty();
      }
  };
  
  /**
   * Your MyStack object will be instantiated and called as such:
   * MyStack* obj = new MyStack();
   * obj->push(x);
   * int param_2 = obj->pop();
   * int param_3 = obj->top();
   * bool param_4 = obj->empty();
   */
  ```

## 有效的括号
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0020.%E6%9C%89%E6%95%88%E7%9A%84%E6%8B%AC%E5%8F%B7.html#%E6%80%9D%E8%B7%AF)
- 题意是给一个只有小中大括号的字符串，判断其是否正确地两两匹配
- 方法是利用栈，若两两匹配则弹出，后面的判断陆爻齐简单地判断栈是否空作为结果，毕竟陆爻齐是把无法匹配的括号也放进去了，代码随想录则分别判断了情况并处理，更为周全
- 陆爻齐的解法
  ```
  class Solution {
  public:
      stack<char> rec;
      bool isValid(string s) {
          if (s.size() % 2 != 0) return false;
  
          for (auto c : s) {
              char top_c = get_top();
              // 匹配成功则弹栈，否则压栈
              if ((c == ')' && '(' == top_c) ||
                  (c == ']' && '[' == top_c) ||
                  (c == '}' && '{' == top_c)) {
                  rec.pop();
              }
              else {
                  rec.push(c);
              }
          }
  
          /*
          if (rec.empty()) {
              return true;
          }
          else {
              return false;
          }*/
          // 若全部匹配成功，则有效
          return rec.empty();
      }
  
      inline char get_top() {
          if (!rec.empty()) {
              return rec.top();
          }
          return ' ';
      }
      
  };
  ```
- 代码随想录的解法
  ```
  class Solution {
  public:
      bool isValid(string s) {
          if (s.size() % 2 != 0) return false; // 如果s的长度为奇数，一定不符合要求
          stack<char> st;
          for (int i = 0; i < s.size(); i++) {
              if (s[i] == '(') st.push(')');
              else if (s[i] == '{') st.push('}');
              else if (s[i] == '[') st.push(']');
              // 第三种情况：遍历字符串匹配的过程中，栈已经为空了，没有匹配的字符了，说明右括号没有找到对应的左括号 return false
              // 第二种情况：遍历字符串匹配的过程中，发现栈里没有我们要匹配的字符。所以return false
              else if (st.empty() || st.top() != s[i]) return false;
              else st.pop(); // st.top() 与 s[i]相等，栈弹出元素
          }
          // 第一种情况：此时我们已经遍历完了字符串，但是栈不为空，说明有相应的左括号没有右括号来匹配，所以return false，否则就return true
          return st.empty();
      }
  };
  ```

## 删除字符串中的所有相邻重复项
- [代码随想录 (programmercarl.com)](https://programmercarl.com/1047.%E5%88%A0%E9%99%A4%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%AD%E7%9A%84%E6%89%80%E6%9C%89%E7%9B%B8%E9%82%BB%E9%87%8D%E5%A4%8D%E9%A1%B9.html)
- 如题，比如“abbaac”，先去 “bb”，得 “aaac”，再去 “aa”，得 “ac”。
- 方法就是用栈，来了与栈顶相同的字符就弹栈，否则压栈
-  陆爻齐参考代码随想录思路的解法
  ```
  class Solution {
  public:
      string removeDuplicates(string s) {
          string result;
          for(auto c : s) {
              if (result.empty() || c != result.back()) {
                  result.push_back(c);
              }
              else {
                  result.pop_back();
              }
          }
          return result;
      }
  };
  ```

## 逆波兰表达式求值
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0150.%E9%80%86%E6%B3%A2%E5%85%B0%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%B1%82%E5%80%BC.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 题意，就是对后缀表达式计算求值，比如 ["2", "1", "+", "3", " * "]
- 方法，就是用栈去保存遍历到的值，一旦遍历到符号，就取出两次顶来做计算然后压栈，具体实现，可以先判断是否符号再处理，也可分别判断，前者代码更简洁，后者判断少一次
- 代码随想录，简洁点
  ```
  class Solution {
  public:
      int evalRPN(vector<string>& tokens) {
          // 力扣修改了后台测试数据，需要用longlong
          stack<long long> st; 
          for (int i = 0; i < tokens.size(); i++) {
              if (tokens[i] == "+" || tokens[i] == "-" || tokens[i] == "*" || tokens[i] == "/") {
                  long long num1 = st.top();
                  st.pop();
                  long long num2 = st.top();
                  st.pop();
                  if (tokens[i] == "+") st.push(num2 + num1);
                  if (tokens[i] == "-") st.push(num2 - num1);
                  if (tokens[i] == "*") st.push(num2 * num1);
                  if (tokens[i] == "/") st.push(num2 / num1);
              } else {
                  st.push(stoll(tokens[i]));
              }
          }
  
          int result = st.top();
          st.pop(); // 把栈里最后一个元素弹出（其实不弹出也没事）
          return result;
      }
  };
  ```
- 陆爻齐，判断少一次
  ```
  class Solution {
  public:
      int evalRPN(vector<string>& tokens) {
          stack<int> s;
          for (auto c : tokens) {
              if (c == "+") {
                  int top1 = s.top();
                  s.pop();
                  int top2 = s.top();
                  s.pop();
                  s.push(top1 + top2);
              }
              else if (c == "-") {
                  int top1 = s.top();
                  s.pop();
                  int top2 = s.top();
                  s.pop();
                  s.push(top2 - top1);
              }
              else if (c == "*") {
                  int top1 = s.top();
                  s.pop();
                  int top2 = s.top();
                  s.pop();
                  s.push(top1 * top2);
              }
              else if (c == "/") {
                  int top1 = s.top();
                  s.pop();
                  int top2 = s.top();
                  s.pop();
                  s.push(top2 / top1);
              }
              else {
                  s.push(stoi(c));
              }
          }
          return s.top();
      }
  };
  ```

## 滑动窗口最大值
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0239.%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3%E6%9C%80%E5%A4%A7%E5%80%BC.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 题意，设定一个窗口大小，然后在 vector 上从左向右滑动，每次都计算一次窗口内最大值并保存
- 方法，最简单的暴力解法，O(n^2)，每次窗口都遍历下窗口内；想让时间复杂度变 O(n)，就需要一个叫做单调队列的数据结构，该队列保证内部数值按降序或升序排列，在 C++ 无标准库实现，本题中可让其按降序，每次找最大值仅需取 front 即可
- PS: 单调队列实现不唯一，本题只看最大值，故把较小的数值直接弹栈，不做保存
-  陆爻齐参考代码随想录思路的解法
  ```
  class Solution {
  public:
      // 实现单调队列
      class MyQueue {
      private:
          deque<int> q;
      public:
          void push(int num) {
              // 由于只关心大值，故小值可直接pop
              while(!q.empty() && num > q.back()) {
                  q.pop_back();
              }
              q.push_back(num);
          }
  
          void pop(int num) {
              if (!q.empty() && num == q.front()) {
                  q.pop_front();
              }
          }
  
          // 保证 front 是最大值
          int front() {
              return q.front();
          }
  
      };
      
      vector<int> maxSlidingWindow(vector<int>& nums, int k) {
          MyQueue q;
          vector<int> result;
          int size = nums.size();
          // 先处理前面 k 个值，并记录一次
          for (int i = 0; i < k; i++) {
              q.push(nums[i]);
          }
          result.push_back(q.front());
          // 滑动窗口
          for (int i = k; i < size; i++) {
              q.push(nums[i]);
              q.pop(nums[i - k]);
              result.push_back(q.front());
          }
          return result;
      }
  };
  ```

## 前K个高频元素
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0347.%E5%89%8DK%E4%B8%AA%E9%AB%98%E9%A2%91%E5%85%83%E7%B4%A0.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 题意就是给一个整型数组，要求给出其中出现频率最高的前 K 个元素
- 最直观的解法，就是用 map 记录频率，导入 vector 再用快速排序，但这么做时间复杂度就达到了 O(n*logn)，还有没有更快的方法呢？
  
  这里就要引入优先级队列，在[[滑动窗口最大值]]其实就有所应用，这次不止要最大值，因此那次实现的优先级队列不好直接应用。  
  
  优先级队列在 C++ 的 STL 中就有实现，priority_queue，本质为堆，默认大顶堆。和  
  
  虽然直观上使用大顶堆来保存前 K 个元素比较合适，但是在本题中使用小顶堆更好，因为小顶堆意味着每次 pop 出的就是最小的元素，这么做就可以反复添加元素并去除多于元素来保证堆内都是最大的一批元素。  
-  陆爻齐参考代码随想录思路的解法
    ```
  class Solution {
  public:
      // 自定义排序
      class my_compare {
      public:
          bool operator() (const pair<int,int> &l, const pair<int,int> &r) {
              return l.second > r.second;
          }
      };
  
      vector<int> topKFrequent(vector<int>& nums, int k) {
          // 统计所有数字的频率
          unordered_map<int, int> rec;
          for (auto i : nums) {
              rec[i]++;
          }
  
          // 小顶堆
          priority_queue<pair<int,int>, vector<pair<int,int>>, my_compare> pri_que;
  
          for (unordered_map<int,int>::iterator it = rec.begin(); it != rec.end(); it++) {
              pri_que.push(*it);
              // 对于小顶堆，总是保持非降序，故加入元素后弹出最小即可保持前 k 个最高频次元素
              if (pri_que.size() > k) {
                  pri_que.pop();
              }
          }
  
          vector<int> result(k);
          for (int i = k-1; i >= 0; i--) {
              result[i] = pri_que.top().first;
              pri_que.pop();
          }
  
          return result;
      }
  };
  ```


# 二叉树
## 二叉树的递归遍历
- [代码随想录 (programmercarl.com)](https://programmercarl.com/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E9%80%92%E5%BD%92%E9%81%8D%E5%8E%86.html#%E6%80%9D%E8%B7%AF)
- 如题，用递归的方式完成二叉树的遍历
- 本题更多的是引入递归的写法，首先确认递归的参数和返回值，然后定好终止条件，最后写好单层递归逻辑
- 本质上不同的是输出结果的顺序，前序时，先输出结果再遍历，中序时输出结果再遍历之间，后序时输出结果在遍历后
- 前序遍历：
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
      vector<int> result;
      vector<int> preorderTraversal(TreeNode* root) {
          pre_val(root);
          return result;
      }
  
      void pre_val(TreeNode* n) {
          if (n == nullptr) return;
          result.push_back(n->val);
          pre_val(n->left);
          pre_val(n->right);
      }
  };
  ```
  中序遍历：  
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
      vector<int> result;
      vector<int> inorderTraversal(TreeNode* root) {
          mid_val(root);
          return result;
      }
  
      void mid_val(TreeNode *n) {
          if (n == nullptr) return;
          mid_val(n->left);
          result.push_back(n->val);
          mid_val(n->right);
      }
  };
  ```
  后序遍历：  
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
      vector<int> result;
      vector<int> postorderTraversal(TreeNode* root) {
          back_val(root);
          return result;
      }
  
      void back_val(TreeNode *root) {
          if (root == nullptr) return;
          back_val(root->left);
          back_val(root->right);
          result.push_back(root->val);
      }
  };
  ```

## 二叉树的迭代遍历
- [代码随想录 (.com)](https://programmercarl.com/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E8%BF%AD%E4%BB%A3%E9%81%8D%E5%8E%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 如题，要用迭代的方式来解决遍历二叉树的问题
- 不同于递归遍历的简洁明了，迭代方式需要用栈来模拟递归的方式
  
  前序遍历则简单点，由于搜寻的点就是马上要处理的点，但中序遍历则要分两部分，一部分是一味地搜寻左子树，另一部分开始处理栈积攒的点并切换到右子树；后序遍历则把前序遍历的 “中右左” 换成 “中左右” 再反转成 “右左中” 即可  
- 前序遍历
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
      vector<int> result;
      vector<int> preorderTraversal(TreeNode* root) {
          TreeNode *p = root;
          stack<TreeNode*> rec;
          if(root != nullptr) rec.push(root);
          while(!rec.empty()) {
              p = rec.top();
              rec.pop();
              result.push_back(p->val);
              // 由于是栈，所以先入右，就会有遍历时先左后右的效果
              if (p->right != nullptr) rec.push(p->right);
              if (p->left != nullptr) rec.push(p->left);
          }
          return result;
      }
  
      
  };
  ```
  中序遍历  
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
      vector<int> result;
      vector<int> inorderTraversal(TreeNode* root) {
          TreeNode *cur = root;
          stack<TreeNode *> rec;
          while (cur!= nullptr || !rec.empty()) {
              if (cur != NULL) {
                  rec.push(cur);
                  cur = cur->left;
              }
              else {
                  cur = rec.top();
                  rec.pop();
                  result.push_back(cur->val);
                  cur = cur->right;
              }
          }
          return result;
      }
  
      
  };
  ```
  后序遍历  
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
      vector<int> result;
      vector<int> postorderTraversal(TreeNode* root) {
          TreeNode *cur = root;
          stack<TreeNode*> rec;
          if (cur != nullptr) rec.push(cur);
  
          while(!rec.empty()) {
              cur = rec.top();
              rec.pop();
              result.push_back(cur->val);
              if (cur->left != nullptr) rec.push(cur->left);
              if (cur->right != nullptr) rec.push(cur->right);
          }
          reverse(result.begin(), result.end());
          return result;
      }
  
      
  };
  ```

## 二叉树的统一迭代遍历法
- [代码随想录 (programmercarl.com)](https://programmercarl.com/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E7%BB%9F%E4%B8%80%E8%BF%AD%E4%BB%A3%E6%B3%95.html#%E6%80%9D%E8%B7%AF)
- 如题，就是用类似递归那样可比较通用的迭代遍历法
- 方法就是在压栈遍历过的节点时，再压入一个空指针，遍历到空指针就意味着要输出这个值了
- 前序遍历
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
      vector<int>result;
      vector<int> preorderTraversal(TreeNode* root) {
          stack<TreeNode*> st;
          if (root != nullptr) st.push(root);
          while (!st.empty()) {
              TreeNode *cur = st.top();
              st.pop();
              // 给遍历过的点一个标记，即在后面压一个空指针，遍历到则说明该读取了
              if (cur != nullptr) {
                  if (cur->right != nullptr) st.push(cur->right);
  
                  if (cur->left != nullptr) st.push(cur->left);
  
                  st.push(cur);
                  st.push(nullptr);
              }
              else {
                  cur = st.top();
                  st.pop();
                  result.push_back(cur->val);
              }
          }
          return result;
      }
  
      
  };
  ```
  中序遍历  
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
      vector<int> result;
      vector<int> inorderTraversal(TreeNode* root) {
          stack<TreeNode*> st;
          if (root != nullptr) st.push(root);
          while (!st.empty()) {
              TreeNode *cur = st.top();
              st.pop();
              // 给遍历过的点一个标记，即在后面压一个空指针，遍历到则说明该读取了
              if (cur != nullptr) {
                  if (cur->right != nullptr) st.push(cur->right);
  
                  st.push(cur);
                  st.push(nullptr);
  
                  if (cur->left != nullptr) st.push(cur->left);
              }
              else {
                  cur = st.top();
                  st.pop();
                  result.push_back(cur->val);
              }
          }
          return result;
      }
  
  };
  ```
  后序遍历  
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
      vector<int> result;
      vector<int> postorderTraversal(TreeNode* root) {
          stack<TreeNode*> st;
          if (root != nullptr) st.push(root);
          while (!st.empty()) {
              TreeNode *cur = st.top();
              st.pop();
              // 给遍历过的点一个标记，即在后面压一个空指针，遍历到则说明该读取了
              if (cur != nullptr) {
                  st.push(cur);
                  st.push(nullptr);
  
                  if (cur->right != nullptr) st.push(cur->right);
  
                  if (cur->left != nullptr) st.push(cur->left);
              }
              else {
                  cur = st.top();
                  st.pop();
                  result.push_back(cur->val);
              }
          }
          return result;
      }
  
      
  };
  ```


