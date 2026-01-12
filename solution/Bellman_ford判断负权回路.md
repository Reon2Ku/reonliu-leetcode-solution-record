# Bellman_ford判断负权回路

## 题目描述

###### 题目描述

某国为促进城市间经济交流，决定对货物运输提供补贴。共有 n 个编号为 1 到 n 的城市，通过道路网络连接，网络中的道路仅允许从某个城市单向通行到另一个城市，不能反向通行。



网络中的道路都有各自的运输成本和政府补贴，**道路的权值计算方式为：运输成本 - 政府补贴**。权值为正表示扣除了政府补贴后运输货物仍需支付的费用；权值为负则表示政府的补贴超过了支出的运输成本，实际表现为运输过程中还能赚取一定的收益。



然而，在评估从城市 1 到城市 n 的所有可能路径中综合政府补贴后的最低运输成本时，存在一种情况：**图中可能出现负权回路。**负权回路是指一系列道路的总权值为负，这样的回路使得通过反复经过回路中的道路，理论上可以无限地减少总成本或无限地增加总收益。为了避免货物运输商采用负权回路这种情况无限的赚取政府补贴，算法还需检测这种特殊情况。



请找出从城市 1 到城市 n 的所有可能路径中，综合政府补贴后的最低运输成本。同时能够检测并适当处理负权回路的存在。



**城市 1 到城市 n 之间可能会出现没有路径的情况**

###### 输入描述

第一行包含两个正整数，第一个正整数 n 表示该国一共有 n 个城市，第二个整数 m 表示这些城市中共有 m 条道路。 

接下来为 m 行，每行包括三个整数，s、t 和 v，表示 s 号城市运输货物到达 t 号城市，道路权值为 v。

###### 输出描述

如果没有发现负权回路，则输出一个整数，表示从城市 `1` 到城市 `n` 的最低运输成本（包括政府补贴）。如果该整数是负数，则表示实现了盈利。如果发现了负权回路的存在，则输出 "circle"。如果从城市 1 无法到达城市 n，则输出 "unconnected"。

###### 输入示例

```
4 4
1 2 -1
2 3 1
3 1 -1 
3 4 1
```

###### 输出示例

```
circle
```

###### 提示信息

路径中存在负权回路，从 1 -> 2 -> 3 -> 1，总权值为 -1，理论上货物运输商可以在该回路无限循环赚取政府补贴，所以输出 "circle" 表示已经检测出了该种情况。



数据范围：

1 <= n <= 1000；
1 <= m <= 10000;

-100 <= v <= 100;



## 题解

已知在无负权回路的题目中，bellman_ford算法使用n-1次松弛之后，minDist数组将不再发生变化。如果存在负权回路，那么这个minDist数组将会无限制的小下去。因此我们只需要额外进行一次松弛操作，观察minDist是否发生变化，若变化则存在circle

~~~
#include <iostream>
using namespace std;
#include <climits>
#include <vector>

int main() {
  int n, m;
  cin >> n >> m;
  vector<vector<int>> times(m + 1, vector<int>(3, 0));
  for (int i = 1; i <= m; i++) {
    cin >> times[i][0] >> times[i][1] >> times[i][2];
  }
  vector<bool> visited(n + 1, 0);
  visited[1] = 1;
  vector<int> minDist(n + 1, INT_MAX);
  minDist[1] = 0;
  bool flag = false; //无负权回路
  for (int i = 1; i <= n - 1; i++)
  //此处松弛了n-1次。
  //因为n-1次循环后，所有的节点都已经是访问过的状态了。
  //松弛n次，n+1次，minDist数组都将不再发生变化。
  {
    for (int j = 1; j <= m; j++) {
      if (!visited[times[j][0]]) continue;
      minDist[times[j][1]] = min(minDist[times[j][1]], minDist[times[j][0]] + times[j][2]);
      //这个取最小值的过程就叫松弛
      visited[times[j][1]] = 1;
    }
  }
  for(int j=1;j<=m;j++)
  {
    if(!visited[times[j][0]]) continue;
    if(minDist[times[j][0]]+times[j][2]<minDist[times[j][1]])
    {
        flag = true;
        break;
    }
  }
  if(flag) cout<<"circle";
  else if (minDist[n] == INT_MAX) cout << "unconnected";
  else cout << minDist[n];
}
~~~

以下内容并非我亲自敲过，纯copy

## 拓展

本题可不可 使用 队列优化版的bellman_ford（SPFA）呢？

上面的解法中，我们对所有边松弛了n-1次后，在松弛一次，如果出现minDist出现变化就判断有负权回路。

如果使用 SPFA 那么节点都是进队列的，那么节点进入队列几次后 足够判断该图是否有负权回路呢？

在 [0094.城市间货物运输I-SPFA](https://programmercarl.com/kamacoder/0094.城市间货物运输I-SPFA) 中，我们讲过 在极端情况下，即：所有节点都与其他节点相连，每个节点的入度为 n-1 （n为节点数量），所以每个节点最多加入 n-1 次队列。

那么如果节点加入队列的次数 超过了 n-1次 ，那么该图就一定有负权回路。

所以本题也是可以使用 SPFA 来做的。 代码如下：

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <list>
#include <climits>
using namespace std;

struct Edge { //邻接表
    int to;  // 链接的节点
    int val; // 边的权重

    Edge(int t, int w): to(t), val(w) {}  // 构造函数
};

int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<list<Edge>> grid(n + 1); // 邻接表

    // 将所有边保存起来
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid[p1].push_back(Edge(p2, val));
    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;

    queue<int> que;
    que.push(start); // 队列里放入起点 
    
    vector<int> count(n+1, 0); // 记录节点加入队列几次
    count[start]++;

    bool flag = false;
    while (!que.empty()) {

        int node = que.front(); que.pop();

        for (Edge edge : grid[node]) {
            int from = node;
            int to = edge.to;
            int value = edge.val;
            if (minDist[to] > minDist[from] + value) { // 开始松弛
                minDist[to] = minDist[from] + value;
                que.push(to);
                count[to]++; 
                if (count[to] == n) {// 如果加入队列次数超过 n-1次 就说明该图与负权回路
                    flag = true;
                    while (!que.empty()) que.pop();
                    break;
                }
            }
        }
    }
    if (flag) cout << "circle" << endl;
    else if (minDist[end] == INT_MAX) {
        cout << "unconnected" << endl;
    } else {
        cout << minDist[end] << endl;
    }
}
```



以上代码看上去没问题，但提交之后会发现报错了，为什么呢？

例如这组数据：

```text
4 6
1 4 3
1 2 1
1 3 1
3 2 -2
2 4 1
3 4 0
```

如图：

![img](https://file1.kamacoder.com/i/algo/2025-06-03_16-12-41.jpg)

上面代码在执行的过程中，节点4 已经出队列了，但 计算入度会反复计算 节点4的入度，导致 误判 该图有环。

我们需要记录哪些节点已经出队列了，哪些节点在队列里面，对于已经出队列的节点不用统计入度，以下为优化后的代码：

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <list>
#include <climits>
using namespace std;

struct Edge { //邻接表
    int to;  // 链接的节点
    int val; // 边的权重

    Edge(int t, int w): to(t), val(w) {}  // 构造函数
};


int main() {
    int n, m, p1, p2, val;
    cin >> n >> m;

    vector<list<Edge>> grid(n + 1); // 邻接表

    // 将所有边保存起来
    for(int i = 0; i < m; i++){
        cin >> p1 >> p2 >> val;
        // p1 指向 p2，权值为 val
        grid[p1].push_back(Edge(p2, val));
    }
    int start = 1;  // 起点
    int end = n;    // 终点

    vector<int> minDist(n + 1 , INT_MAX);
    minDist[start] = 0;

    queue<int> que;
    que.push(start); // 队列里放入起点

    vector<int> count(n+1, 0); // 记录节点加入队列几次
    count[start]++;
    vector<bool> inQueue(n+1, false);  // 记录节点是否在队列中

    bool flag = false;
    while (!que.empty()) {

        int node = que.front(); que.pop();
        inQueue[node] = false;  // 节点出队

        for (Edge edge : grid[node]) {
            int from = node;
            int to = edge.to;
            int value = edge.val;
            if (minDist[to] > minDist[from] + value) { // 开始松弛
                minDist[to] = minDist[from] + value;
                 if (!inQueue[to]) {  // 避免重复入队
                    que.push(to);
                    inQueue[to] = true;
                    count[to]++;
                    if (count[to] == n) {// 如果加入队列次数超过 n-1次 就说明该图与负权回路
                        flag = true;
                        while (!que.empty()) que.pop();
                        break;
                    }
                 }
            }
        }
    }

    if (flag) cout << "circle" << endl;
    else if (minDist[end] == INT_MAX) {
        cout << "unconnected" << endl;
    } else {
        cout << minDist[end] << endl;
    }
}
```