---
title: 【Weekly Algorithm】 算法周记之《代码随想录》数组
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 1709b771
date: 2024-09-15 12:03:00
---
# 学习小结
前两周跟随《代码随想录》，对数组的部分做学习，具体而言，学习了二分查找、前缀和、双指针、滑动窗口的方法，来解决相关数组问题。

也学到要坚持循环不变原则，即有些原则在循环中应保持不变，以统一后续操作，方便完成循环，具体可查看螺旋矩阵的部分。

# 数组
## 二分查找
### 正例
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html#%E6%80%BB%E7%BB%93)
- 二分查找重点是搞清楚边界，下面是左闭右开的写法
- 参考代码随想录思路的解法
  ```
  int func(vector<int> nums, int target) {
  	int left = 0, right = nums.size(), middle;
      while (left < right) {
      	middle = (left + right) / 2;
          if (nums[middle] > target) right = middle;
          else if (nums[middle] < target) left = middle + 1;
          else return middle;
      }
      return -1;
  }
  ```
- 左闭右开，即 num[left] 在搜索范围内（可能为 target），num[right] 刚好不在，所以中间更新时也有所区别，left 得是 middle + 1，因为 middle 处已确认不是 target

- 倘若考虑闭区间，将 while 改 `while (left <= right)`, right 改 `right = nums.size() - 1`，`right = middle - 1`即可

### 补充例题
#### leetcode_查找数组中元素的第一个和最后一个位置
- [代码随想录](https://programmercarl.com/0034.%E5%9C%A8%E6%8E%92%E5%BA%8F%E6%95%B0%E7%BB%84%E4%B8%AD%E6%9F%A5%E6%89%BE%E5%85%83%E7%B4%A0%E7%9A%84%E7%AC%AC%E4%B8%80%E4%B8%AA%E5%92%8C%E6%9C%80%E5%90%8E%E4%B8%80%E4%B8%AA%E4%BD%8D%E7%BD%AE.html)
- 首先，可以用两次二分法确认元素的左右边界
  
  最重要的是，分清查找后的三种情况  
	1. 元素大小在数组外，自然不在数组中，比如数组中最大的是9，target 是 12；
	2. 元素大小在数组内，但不在数组中，比如target 是 5，但数组为……4，6……；
	3. 元素在数组中  
  
如此一来，可以设置变量，检测二分法找边界时，是否有更新边界，两次二分法有一次没有更新边界的情况，都说明元素大小在数组外  
  
当两次都更新了边界，就可以看看给出的边界是否时target，不是的话属于第二种情况

```
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int left = searchLeft(nums, target), right = searchRight(nums, target);
        vector<int>result(2);
        // 处理数在外的的情况
        if (left == -2 || right == -2) {
            result[0] = -1; result[1] = -1;
            return result;
        }
        // 处理数在不在其中但大小在中间的情况
        else if (nums[left] != target) {
            result[0] = -1; result[1] = -1;
            return result;
        }
        // 处理数在其中的情况
        result[0] = left, result[1] = right - 1;
        return result;

    }
	
    // 找到左边界的二分法
    int searchLeft(vector<int>& nums, int target) {
        int left = 0, right = nums.size(), middle = 0;
        int leftBoarder = -2;

        while (left < right) {
            middle = (left + right) / 2;
            if (nums[middle] < target) {
                left = middle + 1; 
            }
            else {
                right = middle;
                leftBoarder = right;
            }
        }
        //cout << leftBoarder << endl;
        return leftBoarder;
    }
	
    // 找到右边界的二分法
    int searchRight(vector<int>& nums, int target) {
        int left = 0, right = nums.size(), middle = 0;
        int rightBoarder = -2;

        while (left < right) {
            middle = (left + right) / 2;
            if (nums[middle] <= target) {
                left = middle + 1;
                rightBoarder = left;
            } 
            else right = middle; 
        }
        //cout << rightBoarder << endl;
        return rightBoarder;
    }
};
```

#### leetcode_x的平方根
- [69. x 的平方根 - 力扣（LeetCode）](https://leetcode.cn/problems/sqrtx/description/)
- 即找到平方为 x 的平方根的整数部分
- 毕竟就是算术平方根，就用二分法逼近即可，注意 middle * middle 可能超 int，用 long long 就行
```
class Solution {
public:
    int mySqrt(int x) {
        int result = 0, left = 0, right = x + 1; 
        unsigned long long middle = 0;
		// 二分试那个数平方恰小于等于的
        while(left < right) {
            middle = (left + right) / 2;
            if (middle * middle > x) {
                right = middle;
            }
            else {
                left = middle + 1;
                result = middle;
            }
        }
        return result;
    }
};
```

#### leetcode_有效的完全平方数
- [367. 有效的完全平方数 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-perfect-square/description/)
- 即判断当前数是否为完全平方数，二分法找个平方为该数即可
- 与上个平方类似，不多说， [[leetcode_x的平方根]]
```
class Solution {
public:
    bool isPerfectSquare(int num) {
        unsigned long long left = 0, right = num, middle = 0;
        // 二分法试出那个平方为 num 的数
        while (left <= right) {
            middle = (left + right) / 2;
            if (middle * middle > num) {
                right = middle - 1;
            }
            else if (middle * middle < num) {
                left = middle + 1;
            }
            else {
                return true;
            }
        }

        return false;
    }
};
```

## 移除元素
### 正例
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0027.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
- 暴力解法
  即每次遇到要删除的元素，就把数组后面每一位向左覆盖一位  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int removeElement(vector<int>& nums, int val) {
          int size = nums.size();
          for (int i = 0; i < size; i++) {
              if (nums[i] == val) {
                  for (int j = i; j < size - 1; j++) {
                      nums[j] = nums[j + 1];
                  }
                  size--;
                  i--;
              }
          }
  
          return size;
      }
  };
  ```
- 双指针法
  快指针去找元素，慢指针是新数组的元素  
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int removeElement(vector<int>& nums, int val) {
          int fast_p = 0, slow_p = 0;
          int size = nums.size();
          for (fast_p = 0; fast_p < size; fast_p++) {
              if (nums[fast_p] != val) {
                  nums[slow_p++] = nums[fast_p];
              }
          }
  
          return slow_p;
      }
  };
  ```
### 补充例题
#### leetcode_重复删除有序数组中的重复项
- [26. 删除有序数组中的重复项 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)
- 即数组去重
- 双指针秒了
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int removeDuplicates(vector<int>& nums) {
          int size = nums.size(), slow = 0, fast = 0;
          int counter = 1;
          for (fast = 1; fast < size; fast++) {
          	// 不同的情况做个覆盖
              if (nums[slow] != nums[fast]) {
                  nums[++slow] = nums[fast];
                  counter++;
              }
          }
          return counter;
      }
  };
  ```

#### leetcode_移动零
- [283. 移动零 - 力扣（LeetCode）](https://leetcode.cn/problems/move-zeroes/description/)
- 即保持非零数字顺序，零全放右侧
- 双指针解决，记得有个 swap 函数可用于向量就方便些，简单地说，就是让快指针去找非零数，然后替换到慢指针所在的地方，若快指针遇到零就不换，跳过就是。
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      void moveZeroes(vector<int>& nums) {
          int size = nums.size();
          int slow = 0, fast = 0;
          for (fast = 0; fast < size; fast++) {
          	// 快指针不为 0 时，快慢指针换值并使慢指针右移
              if (nums[fast] != 0) {
                  swap(nums[slow++], nums[fast]);
              }
          }
      }
  };
  ```

  #### leetcode_比较含退格的字符串
  - [844. 比较含退格的字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/backspace-string-compare/description/)
- 该题思路挺多，不过还得是双指针
- 可以用栈来改两个字符串，陆爻齐选择用双指针来改两字符串，也就是用慢指针确认前面的字符是什么，快指针去找要放在字符串的字符，换到慢指针处，与移动零的区别是，遇到 ‘#’ 时，慢指针应向左一格，让下次的字符覆盖上个字符。
- 也有思路从后向前双指针同时比较（退格键的左侧不重要，右侧可确定）
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      bool backspaceCompare(string s, string t) {
          work(s);
          work(t);
          return s==t;
      }
  	
      // 使字符串变成处理完退格字符后的字符串
      void work(string &s) {
          int size = s.size(), slow = 0, fast = 0;
          for (fast = 0; fast < size; fast++) {
              if (s[fast] != '#') {
                  s[slow++] = s[fast];
              }
              else {
                  if (slow > 0) {
                      slow--;
                  }
              }
          }
          s = s.substr(0, slow);
          //cout << s << endl;
      }
  };
  ```

#### leetcode_有序数组的平方
- [977. 有序数组的平方 - 力扣（LeetCode）](https://leetcode.cn/problems/squares-of-a-sorted-array/solutions/2806253/xiang-xiang-shuang-zhi-zhen-cong-da-dao-blda6/)
- 即将非递减的数组（含负数），各位平方后再以非递减序列排成数组
- 从两侧向中间检测，也是双指针罢
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<int> sortedSquares(vector<int>& nums) {
          int left = 0, size = nums.size(), right = size - 1;
          vector<int> result(size);
          for (int i = size - 1; i >= 0; i--) {
          	// 左右比较，选大者放后面，循环至尽
              if (nums[left] * nums[left]> nums[right] * nums[right]) {
                  result[i] = nums[left] * nums[left];
                  left++;
              }
              else {
                  result[i] = nums[right] * nums[right];
                  right--;
              }
          }
  
          return result;
      }
  };
  ```

## 滑动窗口
### 正例
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.html#%E6%80%9D%E8%B7%AF)
- 滑动窗口可以视为双指针的特殊形态，以 [长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/) 为例
- 该题要求一个数组中连续元素和大于 target 的最小长度是多少
- 暴力解法可以用两个 for 循环嵌套，一个相当于起始，一个相当于终止，穷举出所有可能，不过时间复杂度为 O(n^2)
- 那么接下来就是 O(n) 的滑动窗口啦，该方法用 for 循环遍历终止位置，当快指针已遍历的数值满足需求时，记录长度，同时慢指针也向右移动至不满足要求，此时快指针再向右移动找下一个满足需求的情况
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int minSubArrayLen(int target, vector<int>& nums) {
          int size = nums.size(), sum = 0, least_len = INT32_MAX, i = 0, sub_len;
          for (int j = 0; j < size; j++) {
              sum += nums[j];
              while (sum >= target) {
                  sub_len = j - i + 1;
                  if (least_len > sub_len) {
                      least_len = sub_len;
                  }
                  sum -= nums[i];
                  i++;
              }
          }
          return (least_len == INT32_MAX) ? 0 : least_len;
      }
  };
  ```

### 补充例子
#### leetcode_水果成篮
- [904. 水果成篮 - 力扣（LeetCode）](https://leetcode.cn/problems/fruit-into-baskets/)
- 该题就是求数组中最长的连续两数字序列长度
- 要点是运用哈希表来做查重记录工作
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      int totalFruit(vector<int>& fruits) {
          unordered_map<int,int> cns;
          int size = fruits.size();
          int left = 0;
          int max_len = 0;
          for (int right = 0; right < size; right++) {
          	// 记录第一次遇到该数字
              if (cns.find(fruits[right]) == cns.end()) {
                  //cns.insert({fruits[right], 1});
                  cns[fruits[right]] = 1;
              }
              // 重复计数
              else {
                  cns[fruits[right]]++;
              }
  			
              // 当种类超过 2，开始去除一个
              while (cns.size() > 2) {
                  cns[fruits[left]]--;
                  if (cns[fruits[left]] == 0) {
                      cns.erase(fruits[left]);
                  }
                  left++;
              }
  			
              // 更新值
              max_len = max(max_len, right - left + 1);
          }
          return max_len;
      }
  };
  ```

#### leetcode_最小覆盖子串
- [76. 最小覆盖子串 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-window-substring/)
- 该题就是在字符串 s 上，找涵盖字符串 t 所有字符的最小字符子串
- 只好用两字典存已有字符和目标字符，其余还是沿用滑动窗口，不过效率很低
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      unordered_map<char, int> cns, target;
      string minWindow(string s, string t) {
          string result = "";
          int min_len = INT32_MAX, start_ind = 0;
  
          // 初始化 target 和 cns
          for(auto it : t) {
              target[it]++;
              cns[it] = 0;
          }
  
          int left = 0, right = 0;
          int size = s.size();
  
          for (right = 0; right < size; right++) {
              if (cns.find(s[right]) != cns.end()) {
                  cns[s[right]]++;
              }
              
              // 满足就调整左边窗口
              while (check()) {
                  if (right - left + 1 < min_len) {
                      //cout << "left " << left << ";" << "right " << right << endl;
                      start_ind = left;
                      min_len = right - left + 1;
                  }
                  
                  if (cns.find(s[left]) != cns.end()) {
                      cns[s[left]]--;
                  }
                  left++;
              }
          }
  
          // 排除无结果的错误
          if (min_len == INT32_MAX) {
              min_len = 0;
          }
          result = s.substr(start_ind, min_len);
          return result;
      }
  
      // 检查已有条件是否满足
      bool check() {
          for (auto &it : cns) {
              if (it.second < target[it.first]) {
                  return false;
              }
          }
          return true;
      }
      
  };
  ```

## 螺旋矩阵
### 正例
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0059.%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5II.html#%E6%80%9D%E8%B7%AF)
- 即将数组以某种特定方式赋值排列
- 没有特定的方法，重点是坚持循环不变原则，即有些处理时统一方法，比如螺旋矩阵换方向赋值时，可坚持左闭右开的原则赋值
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<vector<int>> generateMatrix(int n) {
          vector<vector<int>> res = vector(n, vector<int>(n, 0));
          int loop = n / 2; // 主循环次数
          int middle = n / 2; // 记录矩阵中间的值
          int startx = 0, starty = 0; // 记录每次循环的起点/终点
          int set_num = 1; // 每次放入的数字
          int off_set = 1; // 控制每循环填数个数
          int i, j;
  
          while(loop-- >= 0) {
              i = startx, j = starty;
              // 左上到右上
              for (;j < n - off_set; j++) {
                  res[i][j] = set_num++;
              }
              // 右上到右下
              for (;i < n - off_set; i++) {
                  res[i][j] = set_num++;
              }
              // 右下到左下
              for (;j > starty; j--) {
                  res[i][j] = set_num++;
              }
              // 左下到左上
              for (;i > startx; i--) {
                  res[i][j] = set_num++;
              }
  
              
              startx++;
              starty++;
              off_set++;
          }
  
          // 对奇数矩阵补中间
          if (n % 2 != 0) {
              res[middle][middle] = set_num;
          }
  
          return res;
      }
  };
  ```

### 补充例子
#### leetcode_螺旋遍历二维数组
- [54. 螺旋矩阵 - 力扣（LeetCode）](https://leetcode.cn/problems/spiral-matrix/description/)
- 该题要将一个 m*n 的矩阵顺时针输出其中的内容
- 代码随想录没解，暂时也没想到那个思路的延申
- 可借鉴思路是，每次按向右，向下，向左，向上四次输出到头，每个方向走完，判断下下个方向是否还有路，无则马上退出
- 参考代码随想录思路的解法
  ```
  class Solution {
  public:
      vector<int> spiralOrder(vector<vector<int>>& matrix) {
          int m = matrix.size(), n = matrix[0].size();
          vector<int> res;
          
          int u = 0, d = m - 1; // 上下
          int l = 0, r = n - 1; // 左右
  
          while (true) {
              for (int i = l; i < r + 1; i++) {
                  res.push_back(matrix[u][i]); // 向右
              }
              if (++u > d) break;// 上边界大于下边界
              for (int i = u; i < d + 1; i++) {
                  res.push_back(matrix[i][r]); // 向下
              }
              if (--r < l) break; // 右边界左于左边界
              for (int i = r; i > l - 1; i--) {
                  res.push_back(matrix[d][i]); // 向左
              }
              if (--d < u) break; // 下边界高于上边界
              for (int i = d; i > u - 1; i--) {
                  res.push_back(matrix[i][l]); // 向上
              }
              if (++l > r) break; // 左边界右于右边界
          }
          
          return res;
      }
  };
  ```

## 区间和
### 正例
- [代码随想录 (programmercarl.com)](https://programmercarl.com/0977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.html#%E6%80%9D%E8%B7%AF)
- [58. 区间和（第九期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1070)
- 就是要短时间内多次求一个数组上一段区间的数之和
- 要用到前缀和，也就是在初始化数组时，用另一个数组存当前下标之前所有数字的和（每次存新数就更新累加和即可）
- 最后求区间和时，用前缀和数组的右下标减左下标即可
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int count = 0,left = 0, right = 0;
      scanf("%d", &count);
      vector<int> num(count), pre_sum(count);
      int store_sum = 0;
      
      // 记录数组同时计算前缀和
      for (int i = 0; i < count; i++) {
          scanf("%d", &num[i]);
          store_sum += num[i];
          pre_sum[i] = store_sum;
      }
      
      // 持续接收左右下标至输入结束
      while(~scanf("%d %d", &left, &right)) {
          if (left == 0) {
              printf("%d\n",pre_sum[right]);
              continue;
          }
          printf("%d\n", pre_sum[right] - pre_sum[left-1]);
      }
      return 0;
  }
  ```

### 补充例子
#### 开发商购买土地
- [44. 开发商购买土地（第五期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1044)
- 给一个 m*n 的数组，可能对每行每列横切/竖切，求两边差值最小值
- 可前缀和记录横向或者竖向的前缀和，然后就可方便大量计算差值
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int m, n;
      cin >> n >> m;
      vector<vector<int>> block(n, vector<int>(m, 0));
      vector<int> horizontal(n, 0);
      vector<int> vertical(m, 0);
      int result = INT32_MAX;
      int sum = 0;
      
      // 初始化，顺便把横向前缀和求完
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> block[i][j];
              horizontal[i] += block[i][j];
              sum += block[i][j];
          }
      }
      
      // 计算垂直前缀和
      for (int j = 0; j < m; j++) {
          for (int i = 0; i < n; i++) {
              vertical[j] += block[i][j];
          }
      }
      
      int horizontal_cut = 0;
      for (int i = 0; i < n; i++) {
          horizontal_cut += horizontal[i];
          result = min(result, abs(sum - horizontal_cut - horizontal_cut));// （sum - horizontal_cut）是一个公司的子区域价值，horizontal_cut是另一公司的子区域价值
      }
      
      int vertical_cut = 0;
      for (int j = 0; j < m; j++) {
          vertical_cut += vertical[j];
          result = min(result, abs(sum - vertical_cut - vertical_cut));
      }
      cout << result << endl;
      
  }
  ```
