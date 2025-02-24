---
title: 【Weekly Algorithm】算法周记之《代码随想录》图论（三）
tags:
  - Algorithm
index_img: /img/index/leetcode.png
hide: false
abbrlink: 8659ba4e
date: 2024-02-23 23:31:00
---
# 学习小结
本周跟随《代码随想录》继续学习关于图论的知识，主要是关于并查集以及最小生成树的两个经典算法（prim和kruskal）。

并查集的作用在于快速区分不同的类别并补充内容。prim 则是从点的角度从图中划分出最小生成树，kruskal 是划分边来种最小生成树。

# 图论
- ## 有向图的完全可达性
- [代码随想录](https://www.programmercarl.com/kamacoder/0105.%E6%9C%89%E5%90%91%E5%9B%BE%E7%9A%84%E5%AE%8C%E5%85%A8%E5%8F%AF%E8%BE%BE%E6%80%A7.html#%E6%80%9D%E8%B7%AF)
- [105. 有向图的完全可达性](https://kamacoder.com/problempage.php?pid=1177)
- 问题：给一个有向图，但求中的1节点是否能到达其他节点。
- 方法：从 1 开始 dfs/bfs 就可以，重点在于用邻接表存储
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <list>
  
  using namespace std;
  
  
  void dfs(const vector<list<int>> grid, vector<bool> &visited, int &count, int node) {
      count++;
      visited[node] = true;
      // 遍历每个节点所链接的部分
      for (const auto &i : grid[node]) {
          if (visited[i]) continue;
          dfs(grid, visited, count, i);
      }
  }
  
  int main() {
      int n, k;
      cin >> n >> k;
      
      vector<list<int>> grid(n+1);
      
      for (int i = 0; i < k; i++) {
          int a, b;
          cin >> a >> b;
          grid[a].push_back(b);
      }
      
      vector<bool> visited(n+1, false);
      int count = 0;
      
      dfs(grid, visited, count, 1);
      
      int result;
      if (count == n) {
          result = 1;
      }
      else {
          result = -1;
      }
      cout << result << endl;
      
      // 测试输出图
      // for (int i = 0; i < n+1; i++) {
      //     cout << i << ": ";
      //     for (auto &j : grid[i]) {
      //         cout << j << " ";
      //     }
      //     cout << endl;
      // }
      return 0;
  }
  ```
- ## 岛屿的周长
- [代码随想录](https://www.programmercarl.com/kamacoder/0106.%E5%B2%9B%E5%B1%BF%E7%9A%84%E5%91%A8%E9%95%BF.html#%E6%80%9D%E8%B7%AF)
- [106. 岛屿的周长](https://kamacoder.com/problempage.php?pid=1178)
- 题意：二维数组中的岛屿没有内部水，求该一个岛屿的边缘周长。
- 方法：两个思路，一个是遍历岛屿，求每块的边界；第二个思路是求岛屿块数，4*块数 - 相邻区块的重叠边，该思路只要注意求相邻区块的方向不要重合，比如每个区块都只检查左上两个方向。
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  // 一格的四个方向
  int dr[4][2] = {1, 0, -1, 0, 0, 1, 0, -1};
  
  int main() {
      
      int n, m;
      cin >> n >> m;
      
      vector<vector<int>> grid(n, vector<int>(m, 0));
      
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              cin >> grid[i][j];
          }
      }
      
      // 遍历统计边
      int edge_num = 0;
      for (int i = 0; i < n; i++) {
          for (int j = 0; j < m; j++) {
              if (grid[i][j] == 0) continue;
              
              for (int k = 0; k < 4; k++) {
                  int nx = i + dr[k][0];
                  int ny = j + dr[k][1];
                  
                  if (nx < 0 || nx >= n || ny < 0 || ny >= m || grid[nx][ny] == 0) {
                      edge_num++;
                  }
              }
              
          }
      }
      
      cout << edge_num << endl;
      
      return 0;
  }
  ```
- ## 寻找存在的路径
- [代码随想录](https://www.programmercarl.com/kamacoder/0107.%E5%AF%BB%E6%89%BE%E5%AD%98%E5%9C%A8%E7%9A%84%E8%B7%AF%E5%BE%84.html)
- [107. 寻找存在的路径](https://kamacoder.com/problempage.php?pid=1179)
- 题意：给一个无向图和两个点，由于无向图不是连通，要判断两点是否连通
- 方法：用并查集，每次输入边就联系两点的并查集。
- 参考代码随想录思路的解法：
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int find(vector<int> &father, int a) {
      return father[a] == a ? a : father[a] = find(father, father[a]);// 路径压缩
  }
  
  
  int main() {
      int n, m;
      cin >> n >> m;
      
      // n个点 m条边
      vector<int> father(n+1, -1);
      
      // 初始化并查集
      for (int i = 0; i < n+1; i++) {
          father[i] = i;
      }
      
      for (int i = 0; i < m; i++) {
          int a, b;
          cin >> a >> b;
          a = find(father, a);
          b = find(father, b);
          //cout << a << " " << b << endl;
          if (a != b) father[a] = b;
          
      }
      
      // for (int i = 0; i < n+1; i++) {
      //     cout << father[i] << endl;
      // }
      
      int source, destination;
      cin >> source >> destination;
      
      // 本质上就是两个点是否属于统一个并查集（同一个无向连通图）
      cout << int(find(father, father[source])==find(father, father[destination])) << endl;
      return 0;
  }
  ```
- ## 冗余连接
- [代码随想录](https://www.programmercarl.com/kamacoder/0108.%E5%86%97%E4%BD%99%E8%BF%9E%E6%8E%A5.html#%E6%80%9D%E8%B7%AF)
- [108. 冗余连接](https://kamacoder.com/problempage.php?pid=1181)
- 题意：给一个无向图的构建，但要求返回最后一个连成环的边
- 方法：用并查集，一旦检测出一个边的两个点在同一个并查集，就返回这条边
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int find(vector<int> &father, int a){ // 查找并查集所属类
      return father[a] == a ? a : father[a] = find(father, father[a]);
  }
  
  bool join(vector<int> &father, int a, int b) { // 尝试合并两个点
      a = find(father, a);
      b = find(father, b);
      if (a == b) return false;
      father[a] = b;
      return true;
  }
  
  int main() {
      int n;
      cin >> n;
      vector<int> father(n, 0); // 并查集
      for (int i = 0; i < n; i++) { // 初始化
          father[i] = i;
      }
      
      for (int i = 0; i < n; i++) {
          int a, b;
          cin >> a >> b;
          if (!join(father, a, b)) {
              cout << a << " " << b << endl;
              break;
          }
      }
      
      return 0;
  }
  ```
- ## 冗余连接II
- [代码随想录](https://www.programmercarl.com/kamacoder/0109.%E5%86%97%E4%BD%99%E8%BF%9E%E6%8E%A5II.html#%E6%80%9D%E8%B7%AF)
- [109. 冗余连接II](https://kamacoder.com/problempage.php?pid=1182)
- 题意：[[冗余连接]]的有向图版。
- 方法，由于无向图变成有向图，就不能无脑找那个并查集重复的那条边，这只能避免成环的情况，无法避免一个点有两个父节点的情况。
- 参考代码随想录思路的解法：
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int n;
  vector<int> father(1001, 0);
  
  // 并查集初始化
  void init() {
      for (int i = 1; i <= n; i++) father[i] = i;
  }
  
  // 寻根
  int find(int u) {
      return father[u]==u ? u : father[u] = find(father[u]);
  }
  
  // 查看是否相同
  bool isSame(int p, int q) {
      return find(p) == find(q);
  }
  
  // 加入并查集
  void join(int p, int q) {
      p = find(p);
      q = find(q);
      if (p == q) return;
      father[p] = q;
  }
  
  // 在图里找到要删除的边
  // 原理是从前向后找到第一组同一个并查集的的一组点就行
  void deleteCircleEdge(const vector<vector<int>>& edges) {
      init();
      for (int i = 0; i < n; i++) {
          if (isSame(edges[i][0], edges[i][1])) {
              cout << edges[i][0] << " " << edges[i][1] << endl;
              return;
          }
          else {
              join(edges[i][0], edges[i][1]);
          }
      }
  }
  
  // 判断去除边后剩下能否成树
  bool isDelOk(const vector<vector<int>> edges, int del) {
      init();
      for (int i = 0; i < n; i++) {
          if (i == del) continue;
          if (isSame(edges[i][0], edges[i][1])){ // 出现环说明删除到错的边了，换另一条即可
              return false;
          }
          join(edges[i][0], edges[i][1]);
      }
      return true;
  }
  
  int main() {
      cin >> n;
      
      vector<vector<int>> edges; // 边的记录
      vector<int> inDegree(n+1, 0); // 入度
      for (int i = 0; i < n; i++) {
          int a, b;
          cin >> a >> b;
          edges.push_back({a,b});
          inDegree[b]++;
      }
      
      // 倒序添加入度为2的点对应的边
      vector<int> vec;
      for (int i = n-1; i >= 0; i--) {
          if (inDegree[edges[i][1]] == 2) {
              vec.push_back(i);
          }
      }
      
      // 处理非成环的情况
      if (vec.size() > 0) {
          // 只有两种情况，倒数第一条或者倒数第二条
          // 因为有可能倒数第一条取出后，会产生孤立点，所以去除另一条边
          if (isDelOk(edges, vec[0])) {
              cout << edges[vec[0]][0] << " " << edges[vec[0]][1] << endl;
          }
          else {
              cout << edges[vec[1]][0] << " " << edges[vec[1]][1] << endl;
          }
          return 0;
      }
      
      // 处理有向环
      deleteCircleEdge(edges);
      return 0;
  }
  ```
- ## 最小生成树之prim
- [代码随想录](https://www.programmercarl.com/kamacoder/0053.%E5%AF%BB%E5%AE%9D-prim.html#%E8%A7%A3%E9%A2%98%E6%80%9D%E8%B7%AF)
- [53. 寻宝（第七期模拟笔试）](https://kamacoder.com/problempage.php?pid=1053)
- 题意：本质就是求最小生成树的值大小
- 方法：prim算法
  分三步：  
	1. 找到与最小生成树距离最近的节点（初始化就随便一个都行）
	2. 把节点加入最小生成树
	3. 根据加入节点，更新其他节点与最小生成树的距离

- 但是具体实现还有很多细节，写在了注释中。
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <climits>// INT_MAX
  
  using namespace std;
  
  int main() {
      int v, e;
      cin >> v >> e;
      
      vector<vector<int>> grid(v+1, vector<int>(v+1, 0));
      for (int i = 0; i < e; i++) {
          int x, y, k;
          cin >> x >> y >> k;
          grid[x][y] = k;
          grid[y][x] = k;
      }
      
      vector<int> min_dist(v+1, 10001);// 题目最大值10000
      vector<bool> visited(v+1, false);
      // 找到构成最小生成树的v-1条边
      for (int i = 1; i < v; i++) {
          int min_val = INT_MAX;
          int cur = -1;// 要加入最小生成树的点
          
          for (int j = 1; j <= v; j++) { // 遍历所有点
              // 找一个不在树里面，且与最小生成树距离最小的点
              if (!visited[j] && min_dist[j] < min_val) {
                  min_val = min_dist[j];
                  cur = j;
              }
          }
          
          // 加入最小生成树的点
          visited[cur] = true;
          
          // 遍历与加入点相连的其它点，更新与最小生成树的距离
          for (int i = 1; i <= v; i++) {
              if (visited[i]) continue; // 已在树内
              if (grid[cur][i] == 0) continue; // 无连接
              if (grid[cur][i] < min_dist[i]) {
                  min_dist[i] = grid[cur][i];
              }
          }
      }
      
      int result = 0;
      for (int i = 2; i <= v; i++) { // 之所以i从2开始，是因为min_dist[1]不会赋值
      // 从理解的角度，1一开始就在树内，所以没有计算距离（或是距离为0）
          result += min_dist[i];
      }
      cout << result << endl;
      
      return 0;
  }
  ```
- ## 最小生成树之kruskal
- [代码随想录](https://www.programmercarl.com/kamacoder/0053.%E5%AF%BB%E5%AE%9D-Kruskal.html#%E8%A7%A3%E9%A2%98%E6%80%9D%E8%B7%AF)
- [53. 寻宝（第七期模拟笔试）](https://kamacoder.com/problempage.php?pid=1053)
- 题目：与[[最小生成树之prim]]一样，但方法不同。
- 方法：kruskal看边，而prim看点。简单地说，kruskal会升序遍历边，不断加入不同并查集的边，最后得到最小生成树。
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <climits>// INT_MAX
  #include <algorithm>
  
  using namespace std;
  
  typedef struct _Edge {
      int l;
      int r;
      int val;
  }Edge;
  
  int n = 10001;
  
  vector<int> father(n, 0);
  
  int find(int u) {
      return father[u]==u ? u : father[u] = find(father[u]);
  }
  
  void join(int u, int v) {
      u = find(u);
      v = find(v);
      if (u == v) return;
      father[u] = v;
  }
  
  bool isSame(int u, int v) {
      return find(u) == find(v);
  }
  
  void init() {
      for (int i = 0; i < 10001; i++) {
          father[i] = i;
      }                                                     
  }
  
  
  
  int main() {
      int v, e;
      cin >> v >> e;
      
      vector<Edge> edge;
      
      while(e--) {
          int l, r, val;
          cin >> l >> r >> val;
          edge.push_back({l, r, val});
      }
      
      sort(edge.begin(), edge.end(), [](Edge &e1, Edge &e2) {return e1.val < e2.val;});
      
      init();
      int result = 0;
      
      for (const auto & line : edge) {
          int l = find(line.l);
          int r = find(line.r);
          if (l != r) {
              join(l, r);
              result += line.val;
          }
      }
      
      cout << result << endl;
      
      return 0;
  }
  ```