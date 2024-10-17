---
title: 【Weekly Algorithm】 算法周记之《代码随想录》哈希表
tags: [Algorithm]
index_img: /img/index/leetcode.png
date: 2024-09-29 23:59:00
hide: false
---
# 学习小结
本周跟随《代码随想录》学习哈希表部分，较深入地了解了哈希表的相关算法题，结合数组、set、map完成题目，并利用双指针解决部分题目。

# 哈希表
## 简评
究其本质而言，就是数组，但可以通过给元素编码得其索引，以 O(1) 的效率访存，最适合检测该元素是否存在。
## 有效的字母异位词
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0242.%E6%9C%89%E6%95%88%E7%9A%84%E5%AD%97%E6%AF%8D%E5%BC%82%E4%BD%8D%E8%AF%8D.html)
- 即检测一个字符串是否为另一个字符串的字母打散重组的单词
- 字符可以通过 ASCII 码表来存储，遍历某个字符串，对应字符++，再遍历另一个对应字符--，最后所有字符都为 0 即为字母异位词
-
```
class Solution {
public:
    bool isAnagram(string s, string t) {
        // 记录每个字符对应次数，相当于哈希表
        int word_rec[26] = {0};
        // 遍历某个字符串，记录对应字符出现次数
        for (auto c : s) {
            word_rec[get_index(c)]++;
        }
        // 遍历另一个字符串，把对应字符出现与上个字符串抵消
        for (auto c : t) {
            word_rec[get_index(c)]--;
        }
        // 若有字符没抵消干净，说明不是字母异位词
        for (auto i : word_rec) {
            if (i != 0) {
                return false;
            }
        }
        return true;
    }

    inline int get_index(char c) {
        return int(c)-97;
    }
};
```

## 两个数组的交集
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0349.%E4%B8%A4%E4%B8%AA%E6%95%B0%E7%BB%84%E7%9A%84%E4%BA%A4%E9%9B%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 本题与 [[有效的字母异位词]]，有点类似，不过检测的元素从字符变成数字，而且需要输出重合的部分
- 大致有两思路
	1. 陆爻齐想到，用 unordered_map 先把某一个数组中有的数字记 1，随后遍历另一数组时，把当前 map 里有且值为 1 的部分改值到 2 并放到数组里；
	2. 代码随想录用两个 set，其中一个用来存储某个数组唯一的所有元素，另一个存放结果，由于 set 会保证元素唯一，一旦找到交集就可以无脑 insert 进去

- 这里选择记录陆爻齐的思路
  ```
  class Solution {
  public:
      vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
          unordered_map<int,int> num_rec;
          vector<int> res;
          // 遍历某个数组，记录其中存在数字
          for (auto c : nums1) {
              if (num_rec.find(c) == num_rec.end()) {
                  num_rec[c] = 1;
              }
          }
          // 遍历另一个数组，记录交集数字加入结果
          for (auto c : nums2) {
              if (num_rec.find(c) != num_rec.end() && num_rec[c] == 1) {
                  num_rec[c]++;
                  res.push_back(c);
              }
          }
  
          return res;
      }
  };
  ```

## 快乐数
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0202.%E5%BF%AB%E4%B9%90%E6%95%B0.html#%E6%80%9D%E8%B7%AF)
- 该题就是一个数字游戏，寻找一个整数是否存在一个其各元素平方和为 1 的情况，可能会无限循环
- 解决方法就是创建一个哈希表，把过程中产生的数字记录，若有重复，则陷入死循环，否则到了 1，则为快乐数
-
  ```
  class Solution {
  public:
      bool isHappy(int n) {
          // 记录过程中产生的数
          unordered_map<int, int> record;
          // map 中判断是否记录过的方法
          while (record.find(n) == record.end()) {
              // 做记录
              record[n] = 0;
              n = get_happy(n);
              // 得到快乐数的情况
              if (n == 1) {
                  return true;
              }
              //cout << n << endl;
          }
          return false;
      }
  
      // 递归获取各位平方和
      int get_happy(int n) {
          if (n >= 10) {
              return (n % 10) * (n % 10) + get_happy(n / 10);
          }
          return n * n;
      }
  };
```

## 两数之和
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0001.%E4%B8%A4%E6%95%B0%E4%B9%8B%E5%92%8C.html)
- 该题给予了一个整数数组和目标数字，要求找到数组中一组两数和为目标值的下标
- 暴力解法为 O(n^2) 遍历两层数组，而运用哈希值就能快很多，通过 map，可以把遍历过的数字作为索引，其对应下标作为值存入 map 中，如此在得到数组中的一个数字时，就计算其与目标差值，在 map 中查找差值是否存在，存在则找到解，不存在则存入当前正在遍历的数字及其下标。
-
```
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        map<int,int> record; // 记录已遍历数字，索引为数值，值为下标
        vector<int> result; // 要返回的结果
        int index = 0; // 下标记录

        // 遍历每个数
        for (auto c : nums) {
            int cur = target - c; // 与目标值所差值
            if (record.find(cur) != record.end()) { // 若差值已遍历，则可调出凑出结果
                result.push_back(record[cur]);
                result.push_back(index);
                break;
            }
            else { // 否则记录当前值及其下标
                record[c] = index;
            }

            index++; // 更新下标
        }

        return result;
    }
};
```

## 四数相加||
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0454.%E5%9B%9B%E6%95%B0%E7%9B%B8%E5%8A%A0II.html)
- 与[[两数之和]]类似，不过这次有四个数组，要求找到四个数组各取一数之和为 0 的元组个数
- 方法是，把前两数组之和放 map，和值为 key，出现次数为 value，然后遍历后两数组和，对于其负数若 map 中有，则结果多一个该负数在 map 对应的 value 值
-
```
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> num_record; // 记录前两者和的情况和次数
        int result = 0; // 记录结果元组个数

        // 遍历前两数组，记录和
        for (auto i : nums1) {
            for (auto j : nums2) {
                num_record[i + j]++;
            }
        }

        // 遍历后两数组和，检查是否有满足和为0的一对
        for (auto i : nums3) {
            for (auto j : nums4) {
                if (num_record.find(-1 * (i + j)) != num_record.end()) {
                    result += num_record[-1 * (i + j)];
                }
            }
        }

        return result;
    }
};
```

## 赎金信
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0383.%E8%B5%8E%E9%87%91%E4%BF%A1.html#%E6%80%9D%E8%B7%AF)
- 本题的意思与此前的 [[有效的字母异位词]]，很相近，不过 [[有效的字母异位词]] 需要两个字符串可以互相组成，而本题只需要字符串 a 能由字符串 b 组成即可
- 方法反而简单，可以用 map 记录字母对应次数，但由于本题目已说明字母串中皆为小写字母，所以通过 ASCII 码转换下标记录数组的效率更高，更简单
- 陆爻齐的思路是增加一个整形数字记录所需字母的总个数，一旦字符串 b 中出现所需字母，且所需字母的需要个数非零，则该数字减一，最后秩序检查该数字是否为零即可得知题意是否满足
  
  代码随想录更直接些，先统计字符串b的字母，再用 a 的减去，若出现小于零的位，则说明有没有满足的字母存在  
- 陆爻齐的解法
  ```
  class Solution {
  public:
      int need_num = 0;   // 记录需要字母的总个数
      int word_record[26] = {0}; // 记录需要字母的个数
  
      bool canConstruct(string ransomNote, string magazine) {
          // 记录需要字母个数
          for (auto c : ransomNote) {
              add_rec(get_char_num(c));
          }
  
          // 削减需要字母个数
          for (auto c : magazine) {
              cut_rec(get_char_num(c));
          }
          // 若仍有需要字符未消减干净，则不满足题目条件
          if (need_num > 0){
              return false;
          }
          return true;
      }
  
      // 记录需要的字母及其数量
      void add_rec(int index) {
          word_record[index]++;
          need_num++;
      }
  
      // 削减magazine有的字符，但只有是需要的情况下才削减数量
      void cut_rec(int index) {
          if (word_record[index] > 0) {
              word_record[index]--;
              need_num--;
          }
      }
  
      // 获取字母对应的下标
      inline int get_char_num(char c) {
          return int(c - 97);
      }
  };
  ```
  
  代码随想录的解法  
  ```
  class Solution {
  public:
      bool canConstruct(string ransomNote, string magazine) {
          int record[26] = {0};
          //add
          if (ransomNote.size() > magazine.size()) {
              return false;
          }
          for (int i = 0; i < magazine.length(); i++) {
              // 通过record数据记录 magazine里各个字符出现次数
              record[magazine[i]-'a'] ++;
          }
          for (int j = 0; j < ransomNote.length(); j++) {
              // 遍历ransomNote，在record里对应的字符个数做--操作
              record[ransomNote[j]-'a']--;
              // 如果小于零说明ransomNote里出现的字符，magazine没有
              if(record[ransomNote[j]-'a'] < 0) {
                  return false;
              }
          }
          return true;
      }
  };
  ```

## 三数之和
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0015.%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
- 即在一个数组中，找到下标互不重复且三个值不重复的所有三数之和为零的三元组
- 看起来与[[两数之和]]相近，可以把前两数之和放 map，最后一个数匹配，但由于题目要求返回不重三元组，所以去重将是大难题
- 所以本题采用双指针，先把数组排好序，用 i 确认第一个数 a，再用left 和 right 从 a 的右侧两旁逼近答案
  
  思路不难，但去重难  
  
  首先是 a 的去重，由于排好序了，只要让 a 与前一个值不同，就可以遍历到不同的 a；那么可以让 a 与后面一个不同吗？不能，因为可能有{1，1，-2} 这样的情况，若让 a 与后一个不同，那么由于前两个 1，该答案会被排除。  
  
  然后是 b 和 c 的去重，由于排好序，在取到一个可用值后，让 left 和 right 各向中心去重  
-
  ```
  class Solution {
  public:
      vector<vector<int>> threeSum(vector<int>& nums) {
          int size = nums.size();
          vector<vector<int>> result;
          sort(nums.begin(), nums.end());// 先排好序
          for (int i = 0; i < size; i++) {
  
              // 若最小的数都为正数，那么三数之和不可能为零
              if (nums[i] > 0) {
                  break;
              }
  
              // 对第一个 a 的去重，若前一个与当前相等，那么有三数之和为零的话一定重用，没的话三数之和定不为零
              if (i > 0 && nums[i] == nums[i - 1]) {
                  continue;
              }
  
              // 双指针遍历
              int left = i+1, right = size - 1;
              while (left < right) {
                  int sum = nums[i] + nums[left] + nums[right];
                  // 若三数之和小了，让 left 变大
                  if (sum < 0) {
                      left++;
                  }
                  // 大了，就让 right 变小
                  else if(sum > 0) {
                      right--;
                  }
                  else {
                      result.push_back(vector<int>{nums[i], nums[left], nums[right]});
  
                      // 对于 left 和 right 的去重
                      while (right > left && nums[left] == nums[left + 1]) {
                          left++;
                      }
                      while(right > left && nums[right] == nums[right - 1]) {
                          right--;    
                      }
                      left++;
                      right--;
                  }
                  
              }
          }
          
  
          return result;
      }
  };
  ```

## 四数之和
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0018.%E5%9B%9B%E6%95%B0%E4%B9%8B%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
- 本地算[[三数之和]]的拓展，习得这个拓展套路，便可解决类似的“五数之和”等
- 与 [[四数相加||]] 不同，该题要在与 [[三数之和]] 的一个数组下，找到四数之和为 target 的四元组
- 注意 target 可能为负，所以剪枝不能大于target就剪就差不多了罢
-  ```
  class Solution {
  public:
      vector<vector<int>> fourSum(vector<int>& nums, int target) {
              vector<vector<int>> result;
              sort(nums.begin(), nums.end()); // 确保数组有序
              int size = nums.size();
              for (int k = 0; k < size; k++) { // 第一个数
                  if (nums[k] >= 0 && nums[k] > target) { //第一层剪枝，若往后只会更大则剪
                      break;
                  }
  
                  if (k > 0 && nums[k-1] == nums[k]) { // 第一层去重
                      continue;
                  }
  
                  for (int i = k + 1; i < size; i++) {
                      int i_k_sum = nums[i] + nums[k];
                      if (i_k_sum >= 0 && i_k_sum > target) { // 第二层剪枝
                          break;
                      }
  
                      if (i > k + 1 && nums[i - 1] == nums[i]) { //第二层去重
                          continue;
                      }
  
                      int left = i + 1, right = size - 1;
  
                      while (left < right) {
                          if ((long long)i_k_sum + nums[left] + nums[right] > target) { 
                              right--;
                          }
                          else if((long long)i_k_sum + nums[left] + nums[right] < target) {
                              left++;
                          }
                          else {
                              result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
  
                              while(left < right && nums[left] == nums[left+1]) {
                                  left++;
                              }
                              while(left < right && nums[right] == nums[right-1]) {
                                  right--;
                              }
                              left++;
                              right--;
                          }
                      }
                  }
              }
              return result;
  
      }
  };
  ```

