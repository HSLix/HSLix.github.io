---
title: 【Weekly Algorithm】算法周记之《代码随想录》图论（四）
tags:
  - Algorithm
cover: /img/index/programmercarl.png
categories: 算法周记
series: 算法周记
hide: true
abbrlink: 503f932b
date: 2025-03-05 20:58:10
---
# 学习小结
本周跟随代码随想录继续图论相关学习，主要聚焦于从一个点到另一个点的最小距离问题，学习了 dijkstra，bellman_ford，floyd，A* 算法。

相比于此前的算法设计课，更深刻地了解了各个算法的优势和缺点，比如 dijkstra 不能应对 负权 的边，bellman_ford可能会陷入负权回路的陷阱中，A* 不擅长多源距离等。

至此，代码随想录的一刷算是完成力，嘛，明天起就得开始狠狠二刷咯。

# 图论
- ## dijkstra(朴素版)
- [代码随想录](https://www.programmercarl.com/kamacoder/0047.%E5%8F%82%E4%BC%9Adijkstra%E6%9C%B4%E7%B4%A0.html)
- [47. 参加科学大会（第六期模拟笔试）](https://kamacoder.com/problempage.php?pid=1047)
- 题意：给一些节点和节点之间部分路径（有向），求起点到终点的最短路径是多少。
- 方法：其实bfs/dfs应该也行吧，dijkstra 是求一个点到其余点的最短路径距离。
  本质和prim差不多，但prim只要关注点之间的距离就行，而dijkstra要考虑的可就多了，每次更新最短距离不仅要看新纳入点与当前体系的距离，还要看起点到体系边缘的距离。  
  
  但是该方法不能对有负数权值的图使用，比如下面这个  
  ![image.png](../assets/image_1740494367450_0.png)  
  从节点一出发，如果按照算法，走的路线是 1-3-4，最后看到1-2；但上帝视角可以看到，应该走 1-2-3-4。路线错误的原因是，该算法处理不了多段路程权值小于一段路程的情况。  
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  
  using namespace std;
  
  int main() {
      int n, m;
      cin >> n >> m;
      
      vector<vector<int>> grid(n+1, vector<int>(n+1, INT_MAX));
      for (int i = 0; i < m; i++) {
          int s, e, v;
          cin >> s >> e >> v;
          grid[s][e] = v;
      }
      
      vector<bool>visited(n+1, false);
      vector<int>min_dist(n+1, INT_MAX);// 到各点最短距离
      min_dist[1] = 0;// 起点到自己的距离为零
      for (int i = 1; i < n; i++) {
          // 寻找与当前节点最近的的节点
          int cur = 1; // 默认第一个，解决初始化的问题
          int closest_way = INT_MAX;
          for (int i = 1; i <= n; i++) {
              if (!visited[i] && min_dist[i] < closest_way) {
                  cur = i;
                  closest_way = min_dist[i];
              }
          }
          
          // 加入访问列表中
          visited[cur] = true;
          
          // 更新最短距离
          for (int i = 1; i <= n; i++) {
              if (!visited[i] && grid[cur][i] != INT_MAX && min_dist[cur] + grid[cur][i] < min_dist[i]) {
                  min_dist[i] = min_dist[cur] + grid[cur][i];
              }
          }
      }
      if (min_dist[n] == INT_MAX) {
          cout << -1 << endl;
      }
      else {
          cout << min_dist[n] << endl;
      }
  
      
      return 0;
  }
  ```

- ## dijkstra(堆优化版)
- [代码随想录](https://www.programmercarl.com/kamacoder/0047.%E5%8F%82%E4%BC%9Adijkstra%E5%A0%86.html#%E6%80%9D%E8%B7%AF)
- [47. 参加科学大会（第六期模拟笔试）](https://kamacoder.com/problempage.php?pid=1047)
- 题意：与 [[dijkstra(朴素版)]]相同，都是求起点到终点最短距离
- 方法：此处重点阐述该方法与普通迪杰斯特拉的不同，在[[dijkstra(朴素版)]]中，每次都要遍历所有的边来选取与当前体系距离最短的点，堆优化则是引入了小顶堆来解决这个问题，小顶堆保持堆顶是所有边中最小的那个，每次只需要取堆顶即可。C++中通过优先级队列来实现小顶堆。
- 参考代码随想录思路的解法：
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  #include <list>
  #include <queue>
  
  using namespace std;
  
  class my_comparison {
      public:
      bool operator()(const pair<int, int> &lhs, const pair<int, int> &rhs) {
          return lhs.second > rhs.second; // 队尾到队首降序，取出来就是升序
      }
  };
  
  priority_queue<pair<int, int>, vector<pair<int,int>>, my_comparison> pq;
  
  struct Edge {
      int to;// 连接点号
      int val; // 权值
      
      Edge(int a, int b): to(a), val(b) {}
  };
  
  
  
  int main() {
      int n, m;
      cin >> n >> m;
      
      vector<list<Edge>> grid(n+1);
      // 初始化
      for (int i = 0; i < m; i++) {
          int from, to, val;
          cin >> from >> to >> val;
          grid[from].push_back(Edge(to, val));
      }
      
      vector<bool> visited(n+1, false);
      vector<int> min_dist(n+1, INT_MAX);
      
      
      // 初始化优先级队列
      int start = 1;
      int end = n;
      pq.push(make_pair(start, 0));
      min_dist[start] = 0;
      
      // 开始算法
      while(!pq.empty()) {
          // 第一步，取到最近点，取优先级队列队首即可
          pair<int, int> p = pq.top(); 
          pq.pop();
          
          // 去除已访问的点
          if (visited[p.first]) continue;
          
          // 标记取点成功
          visited[p.first] = true;
          
          // 开始遍历当前点所连接的其它点
          for (Edge &e : grid[p.first]) {
              if (!visited[e.to] && e.val + min_dist[p.first] < min_dist[e.to]) {
                  min_dist[e.to] = e.val + min_dist[p.first];
                  pq.push(make_pair(e.to, min_dist[e.to]));
              }
          }
          
      }
      
      if (min_dist[n] != INT_MAX) cout << min_dist[n] << endl;
      else cout << "-1" << endl;
      
      return 0;
  }
  ```

- ## Bellman_ford算法应用
- [代码随想录](https://www.programmercarl.com/kamacoder/0094.%E5%9F%8E%E5%B8%82%E9%97%B4%E8%B4%A7%E7%89%A9%E8%BF%90%E8%BE%93I.html#%E6%80%9D%E8%B7%AF)
- [94. 城市间货物运输 I](https://kamacoder.com/problempage.php?pid=1152)
- 题意：本质是有向图中求起点到终点的最短路程，但是可能出现负权值的边
- 方法：由于有负权值的边，不能用迪杰斯特拉，这里就要用 Bellman_ford 算法，该算法本质是对每条边做 n-1 次的松弛，n是点的数量，松弛指的是根据其它点到该点的距离和权值取小值。与此前的最大不同在于，该算法专注于边，所以grid只要记录每条边的起点、终点和权值即可。详情可查看注释与代码。
  
  但是，该算法不能解决负权回路的问题。  
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  
  using namespace std;
  
  int main() {
      int n, m; 
      cin >> n >> m;
      // 初始化, 用于存储所有的边
      vector<vector<int>> grid;
      
      for (int i = 0; i < m; i++) {
          int s, t, v;
          cin >> s >> t >> v;
          grid.push_back({s, t, v});
      }
      
      // 建立最小距离
      // 对m条边各做n-1次松弛
      vector<int> min_dist(n+1, INT_MAX);
      min_dist[1] = 0;
      
      // 之所以要n-1次，是因为1次松弛能获取起点到该点通过一条边的最小值
      // 每多松弛一次就能多通过一个边
      // n-1 是处理一长条的极端情况
      for (int i = 1; i < n; i++) { // n-1次松弛
          for (auto &e : grid) {
              int from = e[0];
              int to = e[1];
              int val = e[2];
              if (min_dist[from] == INT_MAX) continue; // 该情况说明起点尚未连通
              if (min_dist[to] > min_dist[from] + val) { // 松弛就是更新小值
                  min_dist[to] = min_dist[from] + val;
              }
          }
      }
      
      if (min_dist[n] == INT_MAX) cout << "unconnected" << endl;
      else cout << min_dist[n] << endl;
      return 0;
  }
  ```

- ## Bellman_ford队列优化算法（SPFA）应用
- [代码随想录](https://www.programmercarl.com/kamacoder/0094.%E5%9F%8E%E5%B8%82%E9%97%B4%E8%B4%A7%E7%89%A9%E8%BF%90%E8%BE%93I-SPFA.html#%E8%83%8C%E6%99%AF)
- [94. 城市间货物运输 I](https://kamacoder.com/problempage.php?pid=1152)
- 题意，与 [[Bellman_ford算法应用]]一样，都是在无负权回路的情况下，求起点到终点的最短路径长度。
- 方法，顾名思义，SPFA通过队列一定程度上减少了松弛的次数，在原先的 Bellman_ford算法中，每条边必须要经过 n-1 次松弛，n 为点的数量。显然，很多松弛是不必要的，比如一开始有的点与起点尚未连接就要进行松弛。
  
  所以可以引入队列来保存起点开始的目标点，每次成功松弛，就将目标点放到队列，遍历完一个点所连边，就排除该点（后续可能再次松弛该点）。  
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  #include <list>
  #include <queue>
  
  using namespace std;
  
  struct Edge {
      int to; // 目标
      int val; // 权值
      
      Edge(int a, int b): to(a), val(b) {}
  };
  
  int main() {
      // 初始化
      int n, m;
      cin >> n >> m;
      vector<list<Edge>> grid(n+1);
      for (int i = 0; i < m; i++) {
          int s, t, v;
          cin >> s >> t >> v;
          grid[s].push_back(Edge(t,v));
      }
      
      vector<bool> is_in_queue(n+1, false);
      queue<int> q;// 待松弛边
      vector<int> min_dist(n+1, INT_MAX);
      
      
      q.push(1);
      is_in_queue[1] = true;
      min_dist[1] = 0;
      while(!q.empty()) {
          int node = q.front();
          q.pop();
          
          for (const Edge &e : grid[node]) {
              min_dist[e.to] = min(min_dist[e.to], min_dist[node]+e.val);
              if (!is_in_queue[e.to]) { // 成功松弛就准备遍历目标点
                  q.push(e.to);
                  is_in_queue[e.to] = true;
              }
          }
          
          is_in_queue[node] = false;
      }
      
      if (min_dist[n] == INT_MAX) cout << "unconnected" << endl;
      else cout << min_dist[n] << endl;
      
      return 0;
  }
  ```

- ## Bellman_ford判断负权回路
- [代码随想录](https://www.programmercarl.com/kamacoder/0095.%E5%9F%8E%E5%B8%82%E9%97%B4%E8%B4%A7%E7%89%A9%E8%BF%90%E8%BE%93II.html#%E6%8B%93%E5%B1%95)
- [95. 城市间货物运输 II](https://kamacoder.com/problempage.php?pid=1153)
- 题意，与 [[Bellman_ford算法应用]]类似，但是现在会出现负权回路。
- 方法，要解决负权回路，就要知道一点，在传统的Bellman_ford算法中，每条边最多 n-1 次松弛即可获取结果，此后再多的松弛也不会改变结果。所以，如果一条边松弛次数达到 n 次，说明存在负权回路。
  
  而对于队列优化版的 Bellman_ford 算法，每个点最多进入队列 n-1 次，毕竟每次进队列，都是因为某条连着这个点的边被成功松弛，如果次数到达 n，也说明存在负权回路。  
- 普通Bellman_ford判断负权回路
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  
  using namespace std;
  
  int main() {
      int n, m; 
      cin >> n >> m;
      // 初始化, 用于存储所有的边
      vector<vector<int>> grid;
      
      for (int i = 0; i < m; i++) {
          int s, t, v;
          cin >> s >> t >> v;
          grid.push_back({s, t, v});
      }
      
      // 建立最小距离
      // 对m条边各做n-1次松弛
      vector<int> min_dist(n+1, INT_MAX);
      min_dist[1] = 0;
      bool circle_flag = false;
      
      // 之所以要n-1次，是因为1次松弛能获取起点到该点通过一条边的最小值
      // 每多松弛一次就能多通过一个边
      // n-1 是处理一长条的极端情况
      for (int i = 1; i <= n; i++) { // n-1次松弛，最后一次用来判断负权回路
          for (auto &e : grid) {
              int from = e[0];
              int to = e[1];
              int val = e[2];
              if (min_dist[from] == INT_MAX) continue; // 该情况说明起点尚未连通
              
              if (min_dist[to] > min_dist[from] + val) { // 松弛就是更新小值
                  if (i < n) {
                      min_dist[to] = min_dist[from] + val;
                  }
                  else {
                      circle_flag = true;
                      break;
                  }
              }
          }
          if (circle_flag) break;
      }
      
      if (circle_flag) cout << "circle" << endl;
      else if (min_dist[n] == INT_MAX) cout << "unconnected" << endl;
      else cout << min_dist[n] << endl;
      return 0;
  }
  ```
  SPFA判断负权回路  
  ```
  #include <iostream>
  #include <vector>
  #include <climits>
  #include <list>
  #include <queue>
  
  using namespace std;
  
  struct Edge {
      int to; // 目标
      int val; // 权值
      
      Edge(int a, int b): to(a), val(b) {}
  };
  
  int main() {
      // 初始化
      int n, m;
      cin >> n >> m;
      vector<list<Edge>> grid(n+1);
      for (int i = 0; i < m; i++) {
          int s, t, v;
          cin >> s >> t >> v;
          grid[s].push_back(Edge(t,v));
      }
      
      vector<bool> is_in_queue(n+1, false);
      queue<int> q;// 待松弛边
      vector<int> min_dist(n+1, INT_MAX);
      bool circle_flag = false;
      vector<int> count_release(n+1, 0);
      
      
      q.push(1);
      is_in_queue[1] = true;
      min_dist[1] = 0;
      while(!q.empty()) {
          int node = q.front();
          q.pop();
          
          for (const Edge &e : grid[node]) {
              min_dist[e.to] = min(min_dist[e.to], min_dist[node]+e.val);
              if (!is_in_queue[e.to]) {
                  q.push(e.to);
                  is_in_queue[e.to] = true;
                  count_release[e.to]++;
                  if(count_release[e.to] == n) {
                      circle_flag = true;
                      while(!q.empty()) q.pop();
                      break;
                  }
              }
          }
          
          is_in_queue[node] = false;
      }
      if (circle_flag) cout << "circle" << endl;
      else if (min_dist[n] == INT_MAX) cout << "unconnected" << endl;
      else cout << min_dist[n] << endl;
      
      return 0;
  }
  ```

- ## Bellman_ford之单源有限最短路
- [代码随想录](https://www.programmercarl.com/kamacoder/0096.%E5%9F%8E%E5%B8%82%E9%97%B4%E8%B4%A7%E7%89%A9%E8%BF%90%E8%BE%93III.html#%E6%80%9D%E8%B7%AF)
- [96. 城市间货物运输 III](https://kamacoder.com/problempage.php?pid=1154)
- 题意：在 [[Bellman_ford判断负权回路]]的基础上，加上了最多经过 k 个节点的限制。
- 方法：两个重点
	1. 经过 k 个节点的本质是，对每条边最多k+1次松弛。因为每次松弛就能让起点向外多延申一边；
	2. 其实以往的 Bellman_ford 的实现中，对所有边遍历 n-1 次后，实际上很可能有边超过了 n-1 次松弛。比如 1 与 3 本没有直接连接，但由于对 1 与 2 的连接，使得 min_dist[3] 可以推出。换句话，就是用了本次遍历的结果推出本应下次遍历的结果。之所以之前的题目都没问题，是因为之前的题目中边松弛过多对结果没有影响。解决方法就是每次遍历时记录上一次的遍历结果，基于这个结果来松弛。

- 参考代码随想录思路的解法
  ```
  // 版本一
  #include <iostream>
  #include <vector>
  #include <list>
  #include <climits>
  using namespace std;
  
  int main() {
      int src, dst,k ,p1, p2, val ,m , n;
      
      cin >> n >> m;
  
      vector<vector<int>> grid;
  
      for(int i = 0; i < m; i++){
          cin >> p1 >> p2 >> val;
          // p1 指向 p2，权值为 val
          grid.push_back({p1, p2, val});
      }
  
      cin >> src >> dst >> k;
  
      vector<int> minDist(n + 1 , INT_MAX);
      vector<int> minDist_copy(n+1);// 记录上一次遍历结果
      minDist[src] = 0;
      for (int i = 1; i <= k + 1; i++) { // 对所有边松弛 k + 1次
          minDist_copy = minDist;
          for (vector<int> &side : grid) {
              int from = side[0];
              int to = side[1];
              int price = side[2];
              if (minDist_copy[from] != INT_MAX && minDist[to] > minDist_copy[from] + price) minDist[to] = minDist_copy[from] + price;
          }
  
      }
  
      // for (int i = 0; i <=n; i++) {
      //     cout << minDist[i] << " ";
      // }
      // cout << endl;
  
      if (minDist[dst] == INT_MAX) cout << "unreachable" << endl; // 不能到达终点
      else cout << minDist[dst] << endl; // 到达终点最短路径
  
  }
  
  ```

- ## Floyd算法应用
- [代码随想录](https://www.programmercarl.com/kamacoder/0097.%E5%B0%8F%E6%98%8E%E9%80%9B%E5%85%AC%E5%9B%AD.html#%E6%80%9D%E8%B7%AF)
- [97. 小明逛公园](https://kamacoder.com/problempage.php?pid=1155)
- 题目：给一个无向图，要求给出任意起点终点，能够输出存在的最短的路线的长度。
- 方法：这是多源最短路径求解，之前的 [[dijkstra(朴素版)]]和 [[Bellman_ford算法应用]]都是单源最短路径。Floyd算法不要求边的正负值，其中心思想是动态规划。建立三维数组，dp[i][j][k] val 的含义是，节点 i 到 j 经过 [1,k]的中间节点的最短值为 val。只要从0遍历到 k 层，就能获取所有点到其它点的最短路径。
  
  但是要注意遍历方向，如果去数组按 dp[i][j][k]，那么k的循环必须在最外面一层，而 i 和 j 的顺序无关系。因为初始化时在 dp[i][j][0] 初始化的，必须由 0 层向上遍历才有意义。  
- 参考代码随想录思路的解法
  ```
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  int main() {
      int n, m;
      cin >> n >> m;
  
      vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(n+1, vector<int>(n+1, 10002))); 
      // dp[i][j][k] val 的含义是，节点 i 到 j 经过 [1,k]的中间节点的最短值为 val
      for (int i = 0; i < m; i++) {
          int u, v, w;
          cin >> u >> v >> w;
          dp[u][v][0] = w;
          dp[v][u][0] = w; // 无向图
      }
  
      for (int k = 1; k <= n; k++) {
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j <= n; j++) {
                  dp[i][j][k] = min(dp[i][j][k-1], dp[i][k][k-1] + dp[k][j][k-1]);
              }
          }
      }
  
      int q;
      cin >> q;
      for (int i = 0; i < q; i++) {
          int s, e;
          cin >> s >> e;
          if (dp[s][e][n] == 10002) cout << -1 << endl;
          else cout << dp[s][e][n] << endl;
      }
      return 0;
  }
  ```

- ## A*算法应用
- [代码随想录](https://www.programmercarl.com/kamacoder/0126.%E9%AA%91%E5%A3%AB%E7%9A%84%E6%94%BB%E5%87%BBastar.html#astar)
- [127. 骑士的攻击](https://kamacoder.com/problempage.php?pid=1203)
- 题意：给一个起点和终点，从起点只能按一定规则移动，本题走 日（象棋中马的走法），求最短步数。
- 方法：bfs能解决，但时间消耗比较大，这里就要引入 A* 算法。实际上，A*算法没有定式，本质是 bfs，迪杰斯特拉 等算法的优化，其灵魂在于启发函数，通过这个函数指导高效遍历。
  
  拿本体来说，在 bfs 的基础上，引入优先级队列和启发函数，启发函数用欧拉距离（也可用其它距离表示）衡量目前的点与目标点距离，优先选择离目标更近的点遍历。具体动画可看：[A* && BFS](https://kamacoder.com/tools/knight.html)  
  
  不过，A* 也有缺点，那就是不能像迪杰斯特拉一样对多个点都有遍历距离。如此看来，A*更适合于有明确信息指向目的地且只要单源的情况。  
  
  此外，陆爻齐突发奇想，如果点到起点也用启发函数衡量距离会怎样，结果当然有误。其原因在于，对节点统一衡量为 5，相当于对节点有层数的划分。如果也换启发函数，那么算法会趋近于走两点之间连线的点，这不一定是最佳路线，因为有走法规则限制。比如（5，2）和（5，4），在这个错误方法下要可能会走4步才能到。  
- 参考代码随想录思路的解法
  ```
  #include <cstring>
  #include <iostream>
  #include <queue>
  
  using namespace std;
  
  int a1, a2, b1, b2;
  int dr[8][2] = {1, 2, 2, 1, 2, -1, 1, -2, -1, -2, -2, -1, -2, 1, -1, 2};
  
  int moves[1001][1001];
  
  struct Knight {
    int x, y;
    int g, h, f; // f = g + h
  
    Knight(int a, int b) : x(a), y(b) {}
    Knight() {}
  
    bool operator < (const Knight &o) const{ return o.f < this->f; }
  };
  
  priority_queue<Knight> que;
  
  int dis2begin(Knight &k) {
    return (k.x - a1) * (k.x - a1) + (k.y - a2) * (k.y - a2);
  }
  
  int dis2end(Knight &k) {
    return (k.x - b1) * (k.x - b1) + (k.y - b2) * (k.y - b2);
  }
  
  void bfsastar(Knight &k) {
      Knight cur, next;
      que.push(k);
      while(!que.empty()) {
          cur = que.top(); que.pop();
          if (cur.x == b1 && cur.y == b2) {
              break;
          }
          for (int i = 0; i < 8; i++) {
              next.x = cur.x + dr[i][0];
              next.y = cur.y + dr[i][1];
  
              if (next.x < 1 || next.x > 1000 || next.y < 1 || next.y > 1000) {
                  continue;
              }
  
              if (!moves[next.x][next.y]) {
                  moves[next.x][next.y] = moves[cur.x][cur.y] + 1;
  
                  // 计算f
                  //next.g = dis2begin(next);
                  next.g = cur.g + 5;
                  next.h = dis2end(next);
                  next.f = next.g + next.h;
  
                  que.push(next);
              }
          }
      }
  }
  
  int main() {
    int n;
    cin >> n;
  
    while (n--) {
      memset(moves, 0, sizeof(moves));
  
      cin >> a1 >> a2 >> b1 >> b2;
  
      Knight k(a1, a2);
      k.g = 0;
      k.h = dis2end(k);
      k.f = k.h;
      bfsastar(k);
      while(!que.empty()) que.pop();
      cout << moves[b1][b2] << endl;
    }
    return 0;
  }
  ```