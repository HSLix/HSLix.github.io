---
title: 【Weekly Algorithm】 算法周记之《代码随想录》链表
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-09-22 15:35:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习链表部分，较深入地了解了链表的相关算法题，并利用双指针解决部分题目。

# 链表
## 移除链表元素
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0203.%E7%A7%BB%E9%99%A4%E9%93%BE%E8%A1%A8%E5%85%83%E7%B4%A0.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 由于链表结构特殊，移除操作需要对指针操作，稍微复杂点
- 比较方便的方法是，用一个虚拟的指针头，将这个链表的删除操作转化为统一的链表中元素的移除
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode() : val(0), next(nullptr) {}
   *     ListNode(int x) : val(x), next(nullptr) {}
   *     ListNode(int x, ListNode *next) : val(x), next(next) {}
   * };
   */
  class Solution {
  public:
      ListNode* removeElements(ListNode* head, int val) {
          ListNode* vir_head = new ListNode(0);
          vir_head->next = head;
  
          // 用虚拟头统一操作
          ListNode*p = vir_head;
  
          while(p->next != nullptr) {
              // 下个符合就删
              if (p->next->val == val) {
                  ListNode *tmp = p->next;
                  p->next = p->next->next;
                  delete tmp;
              }
              // 没删 next 的情况再更新到 next，若刚删了 next 且 null，会在循环那里停下来
              else {
                  p = p->next;
              }
          }
  
          return vir_head->next;
      }
  };
  ```
## 设计链表
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0707.%E8%AE%BE%E8%AE%A1%E9%93%BE%E8%A1%A8.html#%E6%80%9D%E8%B7%AF)
- 考察很综合，增删查改
- TODO 独立单链表实现一次
- TODO 双链表独立实现一次
-
- 参考代码随想录思路的解法
  ```
  typedef struct _link {
      int val;
      struct _link *next = nullptr;
      //struct _link *pre = nullptr;
  
      _link(int num) {
          val = num;
      }
  
  }link_node;
  
  class MyLinkedList {
  public:
      int size = 0;
      link_node * head;
  
      MyLinkedList() {
          this->size = 0;
          head = new link_node(0);
      }
      
      int get(int index) {
          if (index >= size || index < 0) {
              return -1;
          }
          link_node *p = head->next;
          for (int i = 0; i < index; i++) {
              p = p->next;
          }
          return p->val;
      }
      
      void addAtHead(int val) {
          link_node *p = new link_node(val);
          p->next = head->next;
          head->next = p;
          this->size++;
      }
      
      void addAtTail(int val) {
          link_node *p = new link_node(val);
          link_node *cur = head;
          while(cur->next != nullptr) {
              cur = cur->next;
          }
          cur->next = p;
          this->size++;
          
          //addAtIndex(this->size, val);
      }
      
      void addAtIndex(int index, int val) {
          if(index > this->size) {
              return;
          }
          if (index < 0) {
              index = 0;
          }
          
          link_node *p = head;
          for (int i = 0; i < index; i++) {
              p = p->next;
          }
  
          link_node *new_node = new link_node(val);
          new_node->next = p->next;
          p->next = new_node;
          this->size++;
  
      }
      
      void deleteAtIndex(int index) {
          if (index >= this->size || index < 0) {
              return;
          }
          link_node *p = head;
          for (int i = 0; i < index; i++) {
              p = p->next;
          }  
          link_node *q = p->next;
          p->next = p->next->next;
          delete  q;
          this->size--;
      }
      
  };
  
  /**
   * Your MyLinkedList object will be instantiated and called as such:
   * MyLinkedList* obj = new MyLinkedList();
   * int param_1 = obj->get(index);
   * obj->addAtHead(val);
   * obj->addAtTail(val);
   * obj->addAtIndex(index,val);
   * obj->deleteAtIndex(index);
   */
  ```

## 翻转链表
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0206.%E7%BF%BB%E8%BD%AC%E9%93%BE%E8%A1%A8.html)
- 本题要求，将一个单链表顺序翻转过来
- 有递归法、双指针法，时间复杂度皆为O（n），双指针空间复杂度O（1），递归O（n）
- 双指针法如下
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode() : val(0), next(nullptr) {}
   *     ListNode(int x) : val(x), next(nullptr) {}
   *     ListNode(int x, ListNode *next) : val(x), next(next) {}
   * };
   */
  class Solution {
  public:
      ListNode* reverseList(ListNode* head) {
          ListNode *pre = nullptr;
          ListNode *cur = head;
          ListNode *tmp = nullptr;// 存储 cur 的下个节点
          while(cur != nullptr) {
              tmp = cur->next; // 先存下个要操作的节点
              cur->next = pre;// 翻转当前节点
              pre = cur; // 整体右移一个
              cur = tmp;
          }
  
          return pre;
      }
  };
  ```

  ## 两两交换链表中的节点
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0024.%E4%B8%A4%E4%B8%A4%E4%BA%A4%E6%8D%A2%E9%93%BE%E8%A1%A8%E4%B8%AD%E7%9A%84%E8%8A%82%E7%82%B9.html)
- 该题就是要求把链表中每两个元素的顺序调换
- 方法就是选定锚点，然后以锚点后两个交换
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode() : val(0), next(nullptr) {}
   *     ListNode(int x) : val(x), next(nullptr) {}
   *     ListNode(int x, ListNode *next) : val(x), next(next) {}
   * };
   */
  class Solution {
  public:
      ListNode* swapPairs(ListNode* head) {
          // 建个哨兵以统一后面的交换进程
          ListNode *setinel = new ListNode(0);
          setinel->next = head;
          ListNode *cur = setinel;
          // 以cur为基准，交换后两个结点
          while(cur->next != nullptr && cur->next->next != nullptr) {
              ListNode *tmp1 = cur->next;
              ListNode *tmp2 = cur->next->next;
  
              // 先连接到后面，剩下两个随意换
              tmp1->next = tmp2->next;
              tmp2->next = tmp1;
              cur->next = tmp2;
  
              // 向右移动两节点
              cur = cur->next->next;
          }
  
          // 由于head可能变了，传setinel回去
          return setinel->next;
      }
  };
  ```

  ## 删除链表的倒数第N个节点
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0019.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9.html)
- 题意易见
- 一次遍历解决的方法是，用双指针，快指针先走N步，再两指针一起走，最后删除慢指针后面那个。
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode() : val(0), next(nullptr) {}
   *     ListNode(int x) : val(x), next(nullptr) {}
   *     ListNode(int x, ListNode *next) : val(x), next(next) {}
   * };
   */
  class Solution {
  public:
      ListNode* removeNthFromEnd(ListNode* head, int n) {
          // 哨兵节点统一处理
          ListNode *sentinel = new ListNode(0);
          sentinel->next = head;
          ListNode *right = sentinel, *left = sentinel;
          // 先走N步
          for (int i = 0; i < n; i++) {
              right = right->next;
          }
  
          // 一起走
          while(right->next != nullptr) {
              right = right->next;
              left = left->next;
          }
  
          // 删慢指针后面那个
          ListNode *tmp = left->next;
          left->next = tmp->next;
          delete tmp;
          return sentinel->next;
      }
  };
  ```

  ## 链表相交
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/%E9%9D%A2%E8%AF%95%E9%A2%9802.07.%E9%93%BE%E8%A1%A8%E7%9B%B8%E4%BA%A4.html)
- 即有两条长短不一的链表，在后面某一点交会到一条，找到交汇的那个点
- 方法即是先找到两个链表的长度，然后让长短两条各有一指针从相同位置同时检测两指针是否相等，毕竟前面的长短不一的部分不可能交汇
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode(int x) : val(x), next(NULL) {}
   * };
   */
  class Solution {
  public:
      ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
          int len_a = 0, len_b = 0;
          ListNode *cur_a = headA, *cur_b = headB;
  
          // 分别测量 a 和 b 的长度
          while(cur_a != nullptr) {
              cur_a = cur_a->next;
              len_a++;
          }
  
          while(cur_b != nullptr) {
              cur_b = cur_b->next;
              len_b++;
          }
          cur_a = headA;
          cur_b = headB;
  
          int len_dif = abs(len_a - len_b);
  
          // 保证 a 是较长的一端
          if (len_a < len_b) {
              swap(cur_a, cur_b);
              swap(len_a, len_b);
          }
  
          // 对齐 a 和 b
          for (int i = 0; i < len_dif; i++) {
              cur_a = cur_a->next;
          }
  
          // 逐一检查是否相等
          while(cur_a != nullptr) {
              if (cur_a == cur_b) {
                  break;
              }
              cur_a = cur_a->next;
              cur_b = cur_b->next;
          }
  
          return cur_a;
      }
  };
  ```
  ## 环形链表||
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0142.%E7%8E%AF%E5%BD%A2%E9%93%BE%E8%A1%A8II.html)
- 即链表后面可能有个环，要求返回入环点
- 两个问题，一、有没有环；二、入环点怎么找
- 有没有环的问题，可以通过简单的双指针解决，即快指针走两步，慢指针走一步，若是有环，一定在某点相遇。倘若慢指针刚入环，无论快指针在环走多少，此时设快指针到慢指针距离 x，每次行动距离减一，迟早相遇。
- 接着是入环点的问题，接下来就是数学为主的时间，设 head 到入环点距离 x，入环点到相遇点距离 y，相遇点到入环点距离 z，慢指针就走了 x+y，快指针走了 x+n* (y+z) +y （毕竟可能走了几圈），快指针移速为慢指针两倍，可列式子 2*(x+y) =  x+n* (y+z) +y，消减得 x = n(y+z) - y，变换得 x = (n-1)(y+z) + z，一看从相遇点开始走 z 步（可能加上好几圈）以及从头开始走的不就可以在入环点相遇了嘛。
  
  假设 n=1，x==z，两边直接相遇；n=2，相遇点处的指针多走一圈，也能相遇  
- 参考代码随想录思路的解法
  ```
  /**
   * Definition for singly-linked list.
   * struct ListNode {
   *     int val;
   *     ListNode *next;
   *     ListNode(int x) : val(x), next(NULL) {}
   * };
   */
  class Solution {
  public:
      ListNode *detectCycle(ListNode *head) {
          ListNode *fast = head, *slow = head;
  
          // 探明是否有环
          while(fast != nullptr && fast->next != nullptr) {
              fast = fast->next->next;
              slow = slow->next;
              if (fast == slow) {
                  // 从交汇点与起点同时走，交汇于入环点
                  slow = head;
                  while (fast != slow) {
                      fast = fast->next;
                      slow = slow->next;
                  }
                  return fast;
              }
          }
          return nullptr;
  
      }
  };
  ```