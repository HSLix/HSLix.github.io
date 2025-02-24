---
title: 【Weekly Algorithm】算法周记之《代码随想录》图论（二）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
date: 2024-02-16 22:11:00
---
# 学习小结
本周跟随《代码随想录》继续学习关于图论的知识，主要是学习岛屿，也就是 bfs/dfs 的经典应用。同时还有[[字符串接龙]]这种新颖的图论题目，才知道各种字符串也能抽象为一个图。

# 图论
- ## 岛屿数量广搜版
- [代码随想录](https://www.programmercarl.com/kamacoder/0099.%E5%B2%9B%E5%B1%BF%E7%9A%84%E6%95%B0%E9%87%8F%E5%B9%BF%E6%90%9C.html#%E6%80%9D%E8%B7%AF)
- [99. 岛屿数量](https://kamacoder.com/problempage.php?pid=1171)
- 直接说明区别和放代码好力，代码就是在[[岛屿数量深搜版]]的基础上做了一点更改，原本的搜索函数不断递归向深了探索，现在是用队列（栈也可，区别在于遍历顺序）来记录要遍历的区块，再按顺序处理
- 修改后的代码
  ```
  #include <iostream>
  #include <vector>
  #include <queue>
  
  using namespace std;
  
  int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1};
  void bfs(const vector<vector<int>> &grid, vector<vector<bool>> &visited, int x, int y) {
      queue<pair<int, int>> q;
      q.push(make_pair(x, y));
      while (!q.empty()) {
          int tmpx = q.front().first;
          int tmpy = q.front().second;
          q.pop();
          for (int i = 0; i < 4; i++) {
              int nextx = tmpx + dir[i][0];
              int nexty = tmpy + dir[i][1];
              if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;
              if (!visited[nextx][nexty] && grid[nextx][nexty] == 1) {
                  visited[nextx][nexty] = true;
                  q.push(make_pair(nextx, nexty));
                  // dfs(grid, visited, nextx, nexty);
              }
          }
      }
      
  }
  
  int main() {
      int n, m;
      cin >> n >> m;
      vector<vector<int>> grid(n, vector<int>(m, 0));
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      vector<vector<bool>> visited(n, vector<bool>(m, false));
      
      int result = 0;
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (!visited[i][j] && grid[i][j] == 1) {
                  visited[i][j] = true;
                  result++;
                  bfs(grid, visited, i, j);
              }
          }
      }
      cout << result << endl;
      return 0;
  }
  ```
- ## 岛屿的最大面积
- [代码随想录](https://www.programmercarl.com/kamacoder/0100.%E5%B2%9B%E5%B1%BF%E7%9A%84%E6%9C%80%E5%A4%A7%E9%9D%A2%E7%A7%AF.html#%E6%80%9D%E8%B7%AF)
- [100. 岛屿的最大面积](https://kamacoder.com/problempage.php?pid=1172)
- 题意：给一个二维数组，求其中纵横相连最大区块面积
- 方法：dfs/bfs
- 从零手搓代码，重点在于visited的更新要及时，这也是易忘点
  第一次把visited放再递归前，少了第一块的计算，故最后会多1  
  下面是最后修正的正确代码  
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int result = 0;
  
  int dr[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
  
  void dfs(const vector<vector<int>> &grid, vector<vector<int>> &visited, int &cur_area, int x, int y) {
      cur_area++;
      visited[x][y] = 1;
      //cout << "visiting:" << x << " " << y << endl;
      for (int i = 0; i < 4; i++) {
          int tmpx = x + dr[i][0];
          int tmpy = y + dr[i][1];
          if (tmpx < 0 || tmpx >= grid.size() || tmpy < 0 || tmpy >= grid[0].size()) continue;
          if (grid[tmpx][tmpy] == 1 && visited[tmpx][tmpy] == 0) {
              dfs(grid, visited, cur_area, tmpx, tmpy);
          }
      }
  }
  
  int main() {
      int n, m;
      
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      vector<vector<int>> visited(grid);
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
  
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              int cur_area = 0;
              if (!(grid[i][j] == 1 && visited[i][j] == 0)) continue;
              dfs(grid, visited, cur_area, i, j);
              if (cur_area > result) result = cur_area;
          }
      }
      
      cout << result << endl;
      
      return 0;
  }
  ```
- ## 孤岛的总面积
- [代码随想录](https://www.programmercarl.com/kamacoder/0101.%E5%AD%A4%E5%B2%9B%E7%9A%84%E6%80%BB%E9%9D%A2%E7%A7%AF.html)
- [101. 孤岛的总面积](https://kamacoder.com/problempage.php?pid=1173)
- 题意：求没有与边缘相连的纵横相连区块的总面积
- 方法：先对边缘的岛屿用dfs/bfs，排除这些区块，然后再遍历中间的面积。
- 代码：
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int dr[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
  
  void dfs(vector<vector<int>> &grid, vector<vector<bool>> &visited, int x, int y) {
      visited[x][y] = true;
      for (int i = 0; i < 4; i++) {
          int nextx = x + dr[i][0];
          int nexty = y + dr[i][1];
          if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;
          if (!visited[nextx][nexty] && grid[nextx][nexty] == 1) {
              dfs(grid, visited, nextx, nexty);
          }
      }
      
  }
  
  int main() {
      int n, m;
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      vector<vector<bool>> visited(n, vector<bool>(m, false));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      for (int i = 0; i < n; i++) {
          if (grid[i][0] == 1 && !visited[i][0]) {
              dfs(grid, visited, i, 0);
          }
          if (grid[i][m-1] && !visited[i][m-1]) {
              dfs(grid, visited, i, m-1);
          }
      }
      
      for (int i = 0; i < m; i++) {
          if (grid[0][i] == 1 && !visited[0][i]) {
              dfs(grid, visited, 0, i);
          }
          if (grid[n-1][i] && !visited[n-1][i]) {
              dfs(grid, visited, n-1, i);
          }
      }
      
      int result = 0;
      for (int i = 1; i < n; i++) {
          for (int j = 1; j < m; j++) {
              if (!visited[i][j] && grid[i][j] == 1) {
                  result++;
              }
          }
      }
      
      cout << result << endl;
      
      return 0;
  }
  ```
- ## 沉没孤岛
- [代码随想录](https://www.programmercarl.com/kamacoder/0102.%E6%B2%89%E6%B2%A1%E5%AD%A4%E5%B2%9B.html)
- [102. 沉没孤岛](https://kamacoder.com/problempage.php?pid=1174)
- 题意：就是求孤岛之外的面积之和
- 方法：[[孤岛的总面积]]的小变式，只要在计算边缘区块时计算面积就行
- 代码：
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int dr[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
  
  void dfs(vector<vector<int>> &grid, vector<vector<bool>> &visited, int x, int y) {
      visited[x][y] = true;
      for (int i = 0; i < 4; i++) {
          int nextx = x + dr[i][0];
          int nexty = y + dr[i][1];
          if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size()) continue;
          if (!visited[nextx][nexty] && grid[nextx][nexty] == 1) {
              dfs(grid, visited, nextx, nexty);
          }
      }
      
  }
  
  int main() {
      int n, m;
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      vector<vector<bool>> visited(n, vector<bool>(m, false));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      for (int i = 0; i < n; i++) {
          if (grid[i][0] == 1 && !visited[i][0]) {
              dfs(grid, visited, i, 0);
          }
          if (grid[i][m-1] && !visited[i][m-1]) {
              dfs(grid, visited, i, m-1);
          }
      }
      
      for (int i = 0; i < m; i++) {
          if (grid[0][i] == 1 && !visited[0][i]) {
              dfs(grid, visited, 0, i);
          }
          if (grid[n-1][i] && !visited[n-1][i]) {
              dfs(grid, visited, n-1, i);
          }
      }
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (visited[i][j] == true) {
                  cout << "1 ";
              }
              else {
                  cout << "0 ";
              }
          }
          cout << endl;
      }
      
      return 0;
  }
  ```
- ## 水流问题
- [代码随想录](https://www.programmercarl.com/kamacoder/0103.%E6%B0%B4%E6%B5%81%E9%97%AE%E9%A2%98.html#%E4%BC%98%E5%8C%96)
- [103. 水流问题](https://kamacoder.com/problempage.php?pid=1175)
- 题意：给一个数组，找其中所有能够到达（左||上）&&（右||下）从高到低（同值也行）的格子
- 方法：普通思路是对每一个格子都做遍历，看是否能到两种边界。但是复杂度过高。反过来，从两种边界逆流遍历，再求两次遍历的交集即可获得格子
- 参考代码随想录思路的代码：
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int n, m;
  int dir[4][2] = {-1, 0, 0, -1, 1, 0, 0, 1};
  void dfs(vector<vector<int>>& grid, vector<vector<bool>>& visited, int x, int y) {
      if (visited[x][y]) return;
  
      visited[x][y] = true;
  
      for (int i = 0; i < 4; i++) {
          int nextx = x + dir[i][0];
          int nexty = y + dir[i][1];
          if (nextx < 0 || nextx >= n || nexty < 0 || nexty >= m) continue;
          if (grid[x][y] > grid[nextx][nexty]) continue; // 注意：这里是从低向高遍历
  
          dfs (grid, visited, nextx, nexty);
      }
      return;
  }
  
  
  int main() {
      
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      // 逆流遍历,这些本质上就是visited
      vector<vector<bool>> firstBoard(n, vector<bool>(m, false));
      vector<vector<bool>> secondBoard(firstBoard);
      
      // 从第一组边界和第二组边界分别逆推
      for (int i = 0; i < n; i++) {
          dfs(grid, firstBoard, i, 0);
          dfs(grid, secondBoard, i, m-1);
      }
      for (int i = 0; i < m; i++) {
          dfs(grid, firstBoard, 0, i);
          dfs(grid, secondBoard, n-1, i);
      }
      
      // 遍历并输出最终结果
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (firstBoard[i][j] && secondBoard[i][j]) {
                  cout << i << " " << j << endl;
              }
          }
      }
      
      return 0;
  }
  
  ```
- ## 建造最大工岛
- [代码随想录](https://www.programmercarl.com/kamacoder/0104.%E5%BB%BA%E9%80%A0%E6%9C%80%E5%A4%A7%E5%B2%9B%E5%B1%BF.html#%E4%BC%98%E5%8C%96%E6%80%9D%E8%B7%AF)
- [104. 建造最大岛屿](https://kamacoder.com/problempage.php?pid=1176)
- 题意：给一个二维数组，沿用上面的岛屿设定，不过本题可以最多填充一块陆地，求填充完最大的陆地面积
- 方法：普通的暴力思路是对每块海洋求四边的陆地和；优化后，先把所有陆地给遍历，求各陆地面积，避免暴力思路中的重复遍历。
  
  实现的重点在于，考虑全陆地的情况，并且要考虑到四边的陆地可能是同一块陆地，所以要去重。  
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <unordered_map>
  #include <unordered_set>
  
  using namespace std;
  
  int n, m;
  
  int dr[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};
  
  void dfs(vector<vector<int>> &grid, vector<vector<bool>> &visited, int x, int y, int &count, int mark) {
      // 加速返回
      if (visited[x][y] || grid[x][y] == 0) return;
      count++;
      visited[x][y] = true;
      grid[x][y] = mark;
      for (int i = 0; i < 4; i++) {
          int nextx = x + dr[i][0];
          int nexty = y + dr[i][1];
          if (nextx < 0 || nextx >= n || nexty < 0 || nexty >= m) continue;
          if (grid[nextx][nexty] == 1 && !visited[nextx][nexty]) {
              dfs(grid, visited, nextx, nexty, count, mark);
          }
      }
  }
  
  int main() {
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
  
      vector<vector<bool>> visited(n, vector<bool>(m, false));
      unordered_map<int, int> gridNum;
      
      int flagNum = 2;
      bool isAllGrid = true;
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (grid[i][j] == 0) isAllGrid = false;
              int count = 0;
              if (!visited[i][j] && grid[i][j] == 1) {
                  dfs(grid, visited, i, j, count, flagNum);
                  gridNum[flagNum] = count;
                  //cout << "flagNum:" << flagNum << "; count:" << count << endl;
                  flagNum++;
              }
          }
      }
      
      if (isAllGrid) {
          cout << n*m << endl;
          return 0;
      }
      
      unordered_set<int> visitGrid;
      int result = 0;
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (grid[i][j] != 0) continue;
              visitGrid.clear();
              int area = 1;
              //cout << "出发：" << i << j << endl;
              for (int k = 0; k < 4; k++) {
                  int nx = i + dr[k][0];
                  int ny = j + dr[k][1];
                  //cout << "查看：" << nx << ny << endl;
                  if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == 0) continue;
                  if (visitGrid.count(grid[nx][ny])) continue;
                  area += gridNum[grid[nx][ny]];
                  //cout << "发现：" << grid[nx][ny] << ";gridNum:" << gridNum[grid[nx][ny]] << ";area:" << area << endl;
                  visitGrid.insert(grid[nx][ny]);
              }
              result = max(result, area);
          }
      }
      cout << result << endl;
      return 0;
  }
  ```
- ## 字符串接龙
- [代码随想录](https://www.programmercarl.com/kamacoder/0110.%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%8E%A5%E9%BE%99.html#%E6%80%9D%E8%B7%AF)
- [110. 字符串接龙](https://kamacoder.com/problempage.php?pid=1183)
- 题意：给一个起始字符串和终止字符串，以及一系列字符串，求起始字符串每次更改一个字符，从这些字符串最少更改几步到终止字符串。
- 方法：看作从起始字符串开始的无向图，向周围bfs（bfs比dfs更方便找最短路径），也就是逐个尝试26字母。
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector> 
  #include <string>
  #include <unordered_set>
  #include <unordered_map>
  #include <queue>
  
  using namespace std;
  
  int main() {
      string beginStr, endStr, str;
      int n;
      cin >> n;
      unordered_set<string> strSet;
      cin >> beginStr >> endStr;
      for (int i = 0; i < n; i++) {
          cin >> str;
          strSet.insert(str);
      }
      
      // 记录字符串是否访问，以及访问到字符串的路径长度
      unordered_map<string, int> visitMap; 
      
      // 为了bfs的队列
      queue<string> que;
      que.push(beginStr);
      
      // 起始的长度为1
      visitMap.insert(make_pair(beginStr, 1));
      
      while(!que.empty()) {
          string word = que.front();
          que.pop();
          int path = visitMap[word];
          
          // 对于word逐个字符尝试替换，然后匹配strSet
          for (int i = 0; i < word.size(); i++) {
              string newWord = word;
              
              // 遍历所有字母
              for (int j = 0; j < 26; j++) {
                  newWord[i] = j + 'a';
                  // 到达重点，直接结束
                  if (newWord == endStr) {
                      cout << path+1 << endl;
                      return 0;
                  }
                  // 与字典匹配且没有匹配过
                  if (strSet.find(newWord) != strSet.end() &&
                  visitMap.find(newWord) == visitMap.end()) {
                      // 记录访问结果，并添加队列
                      visitMap.insert(make_pair(newWord, path+1));
                      que.push(newWord);
                  }
              }
          }
      }
      
      // 无结果
      cout << 0 << endl;
      return 0;
  }
  ```