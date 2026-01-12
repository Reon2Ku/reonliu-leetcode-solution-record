# Bellman_ford队列优化法/SPFA算法

## 题目描述

城市间货物运输 I

###### 题目描述

某国为促进城市间经济交流，决定对货物运输提供补贴。共有 n 个编号为 1 到 n 的城市，通过道路网络连接，网络中的道路仅允许从某个城市单向通行到另一个城市，不能反向通行。



网络中的道路都有各自的运输成本和政府补贴，**道路的权值计算方式为：运输成本 - 政府补贴**。权值为正表示扣除了政府补贴后运输货物仍需支付的费用；权值为负则表示政府的补贴超过了支出的运输成本，实际表现为运输过程中还能赚取一定的收益。



请找出从城市 1 到城市 n 的所有可能路径中，综合政府补贴后的最低运输成本。如果最低运输成本是一个负数，它表示在遵循最优路径的情况下，运输过程中反而能够实现盈利。



**城市 1 到城市 n 之间可能会出现没有路径的情况，同时保证道路网络中不存在任何负权回路。**

###### 输入描述

第一行包含两个正整数，第一个正整数 n 表示该国一共有 n 个城市，第二个整数 m 表示这些城市中共有 m 条道路。 

接下来为 m 行，每行包括三个整数，s、t 和 v，表示 s 号城市运输货物到达 t 号城市，道路权值为 v （单向图）。

###### 输出描述

如果能够从城市 1 到连通到城市 n， 请输出一个整数，表示运输成本。如果该整数是负数，则表示实现了盈利。如果从城市 1 没有路径可达城市 n，请输出 "unconnected"。

###### 输入示例

```
6 7
5 6 -2
1 2 1
5 3 1
2 5 2
2 4 -3
4 6 4
1 3 5
```

###### 输出示例

```
1
```

###### 提示信息

![img](https://file1.kamacoder.com/i/web/20240329112127.png)

示例中最佳路径是从 1 -> 2 -> 5 -> 6，路上的权值分别为 1 2 -2，最终的最低运输成本为 1 + 2 + (-2) = 1。



示例 2：

4 2
1 2 -1
3 4 -1

在此示例中，无法找到一条路径从 1 通往 4，所以此时应该输出 "unconnected"。



数据范围：

1 <= n <= 1000；
1 <= m <= 10000;

-100 <= v <= 100;



## 题解

~~~
#include<iostream>
using namespace std;
#include<vector>
#include<climits>
#include<list>
#include<queue>

struct Edge
{
    int vertex;
    int value;
    Edge(int v,int t):vertex(v),value(t){}
};

int main()
{
    int n,m,fr,ne,v;
    cin>>n>>m;
    vector<list<Edge>> times(n+1);
    //vector<vector<int>> times(m+1,vector<int>(3,0));
    for(int i=1;i<=m;i++)
    {
        cin>>fr>>ne>>v;
        times[fr].push_back(Edge(ne,v));
    }
    vector<bool> visited(n+1,0);
    visited[1] = 1;
    queue<int> que;
    que.push(1);
    vector<int> minDist(n+1,INT_MAX);
    minDist[1] = 0;
    while(!que.empty())
    {
        int top = que.front(); que.pop();
        visited[top] = 0;
        for(Edge edge:times[top])
        {
            minDist[edge.vertex] = min(minDist[edge.vertex],minDist[top]+edge.value);
            if(visited[edge.vertex]) continue;
            else 
            {
                que.push(edge.vertex);
                visited[edge.vertex] = 1;
            }
        }
    }
    if(minDist[n]==INT_MAX) cout<<"unconnected";
    else cout<<minDist[n];
}
~~~



