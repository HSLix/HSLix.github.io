---
title: 【Weekly Algorithm】 算法周记之《代码随想录》字符串
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-10-06 13:45:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习哈希表部分，较深入地了解了字符串的相关算法题，除了双指针，还有 KMP 算法。

# 字符串
## 简评
究其本质而言，就是数组，只不过内容换成了字母。
## 反转字符串
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0344.%E5%8F%8D%E8%BD%AC%E5%AD%97%E7%AC%A6%E4%B8%B2.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 就是把字符串前后翻转，和[[翻转链表]]相似
- 由于字符串可以随机读取，可以简单很多，直接 `swap` i 和 str.size()-1-i 的字符即可
-
  ```
  class Solution {
  public:
      void reverseString(vector<char>& s) {
          int size = s.size();
          for (int i = 0; i < size / 2; i++) {
              swap(s[i],s[size - 1 - i]);
          }
      }
  };
  ```

  ## 反转字符串||
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0541.%E5%8F%8D%E8%BD%AC%E5%AD%97%E7%AC%A6%E4%B8%B2II.html#%E6%80%9D%E8%B7%AF)
- 与 [[反转字符串]] 类似，不过要求每 2k 个字符的前 k 个字符反转
- 方法不难，可以像陆爻齐这样把两个阶段分开，也可像代码随想录这样合一起处理，不过还是要注意循环不变原则，比如反转字符串保持左闭右闭的区间
- 陆爻齐的解法
  ```
  class Solution {
  public:
      string reverseStr(string s, int k) {
          int begin = 0, end = s.size() - 1;
          int size = s.size();
  
          // 先处理了大于 2k 的情况
          while(size > 2 * k) {
              rev_str(s, begin, begin + k - 1);
              begin += 2 * k;
              size -= 2 * k;
          }
  
          // 单独处理小于等于 k 的情况
          if (size >= k) {
              rev_str(s, begin, begin + k - 1);
          }
          else {
              rev_str(s, begin, begin + size - 1);
          }
  
          return s;
      }
  
      // 反转字符串单独实现，左闭右闭
      void rev_str(string &s, int begin, int end) {
          int size = end - begin + 1;
          //cout << "rec: ";
          /*for (int i = begin; i <= end; i++) {
              cout << s[i] << " ";
          }*/
          //cout << endl << endl;
          for (int i = 0; i < size / 2; i++) {
              swap(s[begin + i], s[end - i]);
          }
      }
  };
  ```
- 代码随想录的解法
  ```
  class Solution {
  public:
      void reverse(string& s, int start, int end) {
          for (int i = start, j = end; i < j; i++, j--) {
              swap(s[i], s[j]);
          }
      }
      string reverseStr(string s, int k) {
          for (int i = 0; i < s.size(); i += (2 * k)) {
              // 1. 每隔 2k 个字符的前 k 个字符进行反转
              // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
              if (i + k <= s.size()) {
                  reverse(s, i, i + k - 1);
                  continue;
              }
              // 3. 剩余字符少于 k 个，则将剩余字符全部反转。
              reverse(s, i, s.size() - 1);
          }
          return s;
      }
  };
  ```

  ## 替换数字
  - [替换数字 | 代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0054.%E6%9B%BF%E6%8D%A2%E6%95%B0%E5%AD%97.html#%E6%80%9D%E8%B7%AF)
- 题意是让字符串中的数字，替换为 “number”
- 最普通的方法是从前往后遍历，对于数字，则将后面的字母向后移位，插入 “number”，但时间复杂度为 O(n^2)，如果创建新的字符串来辅助，那么空间复杂度就高了（JAVA 好像不能对字符串直接修改，必须辅助来着）
- 比较好的方法是，先遍历字符串，确认其中数字个数，然后直接对字符串重定大小，从后向前填充，遇到数字就倒着写 number
-
  ```
  #include <iostream>
  #include <string>
  
  using namespace std;
  
  int main() {
      string s;// 存储字符串
      int count;// 记录字符串中数字个数
      
      while(cin >> s) { //只要一直有输入就不会停止
          int ori_size = s.size(); //记录原来大小
          count = 0;
          for(auto c : s) { // 计算数字个数
              if (int(c) < int('a')) {
                  count++;
              }
          }
          
          s.resize(s.size() + 5 * count); // 重置字符串大小
          
          int index = ori_size - 1;// 对原内容遍历下标
          int input_ind = s.size() - 1;// 插入下标
          
          while(input_ind > 0) { // 从后向前遍历
              if (int(s[index]) < int('a')) {  // 对数字从后向前替换numver
                  s[input_ind--] = 'r';
                  s[input_ind--] = 'e';
                  s[input_ind--] = 'b';
                  s[input_ind--] = 'm';
                  s[input_ind--] = 'u';
                  s[input_ind--] = 'n';
              }
              else {
                  s[input_ind--] = s[index]; // 对非数字直接替换
              }
              index--;
          }
          cout << s << endl;
      }
      return 0;
  }
  ```

  ## 翻转字符串里的单词
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0151.%E7%BF%BB%E8%BD%AC%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%87%8C%E7%9A%84%E5%8D%95%E8%AF%8D.html#%E6%80%9D%E8%B7%AF)
- 本题要求只是翻转字符串中每个单词，单词内不翻转，若遇到多个空格，则合并一个
- 整体分三步：
	1. 去除多余空格
	2. 整体翻转
	3. 局部翻转单词

- 第一步可以遍历然后erase，但erase时间复杂度O(n)，这样第一步复杂度O(n^2)，虽然也能过，但有更好的方法；这里就要用到[[移除元素]]中用到的双指针，同样的方法可以消除多余空格，区别就是每次消除前，先自己补个空格
- 第二步和第三步用[[反转字符串]]即可，注意第三步对每个单词反转时，由于默认用左闭右闭的反转函数，遍历的变量 i 是可以取到 s.size() 的。
-
  ```
  class Solution {
  public:
      string reverseWords(string s) {
          removeExactSpaces(s);
          reverseStr(s, 0, s.size() - 1);
  
          int start = 0;
          for (int i = 0; i <= s.size(); i++) { // 由于反转对i-1，i可以取s.size()
              if (s[i] == ' ' || i == s.size()) {
                  reverseStr(s, start, i - 1);
                  start = i+1;
              }
          }
          return s;
      }
  
      // 左闭右闭区间交换
      void reverseStr(string &s, int begin, int end) {
          for (int i = begin, j = end; i < j; i++, j--) {
              swap(s[i], s[j]);
          }
      }
  
      void removeExactSpaces(string &s) {
          // 参考此前的移除元素，双指针O(n)
          int slow = 0;
          int size = s.size();
          for (int i = 0; i < size; i++) {
              if (s[i] != ' ') {
                  if (slow != 0) {
                      s[slow++] = ' ';
                  }
                  while (i < size && s[i] != ' ') {
                      s[slow++] = s[i++];
                  }
              }
          }
          //cout << slow << endl;
          s.resize(slow);
      }
  };
  ```

  ## 右旋字符串
  - [右旋字符串 | 代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0055.%E5%8F%B3%E6%97%8B%E5%AD%97%E7%AC%A6%E4%B8%B2.html)
- 题意就是要求将一个字符串的后 k 个字符放前面
- 不难，最容易想到的方法就是新建个字符串，先把后 k 个放前面，再回头放前面；BUT！！如果不准用辅助空间呢？（又是排除 JAVA 的一集）
- 答案是通过 [[反转字符串]]，整体反转，再将前 k 个（也就是后面 k 个字符反转过来的部分）和后面部分分别反转即可
-
  ```
  #include <iostream>
  #include <string>
  
  using namespace std;
  
  // 左闭右闭反转
  void reverse_str(string &s, int begin, int end) {
      for (int i = begin, j = end; i < j; i++, j--) {
          swap(s[i], s[j]);
      }
  }
  
  int main() {
      string s;
      int k;
      cin >> k >> s;
      int size = s.size();
      reverse_str(s, 0, size-1); // 整体反转
      // 两部分分别反转
      reverse_str(s, 0, k-1);
      reverse_str(s, k, size-1);
      cout << s << endl;
      return 0;
  }
  ```

  ## 实现strStr()
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0028.%E5%AE%9E%E7%8E%B0strStr.html#%E6%80%9D%E8%B7%AF)
- 即求出一个文本串中模板串第一次出现的首字母下标
- 本题利用暴力解法也可 AC，即对每个字符起都循环遍历模板串是否匹配，但想比较好地完成此题，就不得不提到 [[KMP算法]]，本题也就可是KMP匹配成功后立即返回即可
-
  ```
  class Solution {
  public:
      void getNext(int *next, string s) {
          int j = -1;
          next[0] = j;
          int size = s.size();
          for (int i = 1; i < size; i++) { // 循环不变原则，i为后缀末下标，j为前缀末下标
              while (j >= 0 && s[i] != s[j + 1]) {
                  j = next[j];
              }
              if (s[i] == s[j + 1]) {
                  j++;
              }
              next[i] = j;
          }
      }
  
      int strStr(string haystack, string needle) {
          if (needle.size() == 0) {
              return 0;
          }
          
          vector<int> next(needle.size());
          getNext(&next[0], needle);
          int j = -1;
          for(int i = 0; i < haystack.size(); i++) {
              while(j >= 0 && haystack[i] != needle[j + 1]) {
                  j = next[j];
              }
              if (haystack[i] == needle[j + 1]) {
                  j++;
              }
              if (j == (needle.size() - 1)) { // 可理解为结果是 i -
                  return (i - needle.size() + 1);
              }
          }
          return -1;
      }
  };
  ```

  ## 重复的子字符串
  - [代码随想录 (programmercarl.com)](https://programmercarl.com/0459.%E9%87%8D%E5%A4%8D%E7%9A%84%E5%AD%90%E5%AD%97%E7%AC%A6%E4%B8%B2.html#%E6%80%9D%E8%B7%AF)
- 即判断字符串是否由自身的子字符串组成
- 方法大致有三个
	1. 暴力解法，O(n^2)时间复杂度，中间可以只遍历一半字符串优化

  2.移动匹配，把字符串*2拼一起，掐头去尾，若中间有字符串，则是  
	3. [[KMP算法]] ，若KMP找到最长相同前后缀所不包含的串长度可整除字符串，则是

- 移动匹配：
  ```
  class Solution {
  public:
      bool repeatedSubstringPattern(string s) {
          string ss = s + s;
          // 掐头去尾
          ss.erase(ss.begin());
          ss.erase(ss.end() - 1);
          // 如果中间出现原串，说明确实是
          if (ss.find(s) != string::npos) {
              return true;
          }
          return false;
      }
  };
  ```
- KMP
  ```
  class Solution {
  public:
      void getNext(int *next, string s) {
          int j = -1;
          next[0] = j;
          for (int i = 1; i < s.size(); i++) {
              while (j >= 0 && s[i] != s[j + 1]) {
                  j = next[j];
              }
              if (s[i] == s[j + 1]) {
                  j++;
              }
              next[i] = j;
          }
      }
      bool repeatedSubstringPattern(string s) {
          if (s.size() == 0) {
              return false;
          }
          vector<int> next(s.size());
          getNext(&next[0], s);
          int len = s.size();
          // 只要最长前后缀在末尾大于零，且剩余部分长度整除字符串
          if (next[len - 1] != -1 && len % (len - (next[len - 1] + 1)) == 0) {
              return true;
          }
          return false;
      }
  };
  ```
  