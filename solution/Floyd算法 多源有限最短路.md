# Floyd算法 多源有限最短路

## 题目描述

小明逛公园

###### 题目描述

小明喜欢去公园散步，公园内布置了许多的景点，相互之间通过小路连接，小明希望在观看景点的同时，能够节省体力，走最短的路径。 



给定一个公园景点图，图中有 N 个景点（编号为 1 到 N），以及 M 条双向道路连接着这些景点。每条道路上行走的距离都是已知的。



小明有 Q 个观景计划，每个计划都有一个起点 start 和一个终点 end，表示他想从景点 start 前往景点 end。由于小明希望节省体力，他想知道每个观景计划中从起点到终点的最短路径长度。 请你帮助小明计算出每个观景计划的最短路径长度。

###### 输入描述

第一行包含两个整数 N, M, 分别表示景点的数量和道路的数量。 

接下来的 M 行，每行包含三个整数 u, v, w，表示景点 u 和景点 v 之间有一条长度为 w 的双向道路。 

接下里的一行包含一个整数 Q，表示观景计划的数量。 

接下来的 Q 行，每行包含两个整数 start, end，表示一个观景计划的起点和终点。

###### 输出描述

对于每个观景计划，输出一行表示从起点到终点的最短路径长度。如果两个景点之间不存在路径，则输出 -1。

###### 输入示例

```
7 3
2 3 4
3 6 6
4 7 8
2
2 3
3 4
```

###### 输出示例

```
4
-1
```

###### 提示信息

从 2 到 3 的路径长度为 4，3 到 4 之间并没有道路。

1 <= N, M, Q <= 1000.

1 <= w <= 10000.



## 题解

太牛逼了，我佩服的五体投地

Floyd算法不在乎你求多源最短路时，是否有负权重边。

以下代码纯是我手动copy的

首先用三维数组展示遍历顺序。

实际上，k+1的状态只与k有关，我们可以把grid精简为一个二维数组。

~~~
#include<iostream>
using namespace std;
#include<vector>
#include<climits>

int main()
{
    int n,m;
    cin>>n>>m;
    int s,t,val;
    vector<vector<vector<int>>> grid(n+1,vector<vector<int>>(n+1,vector<int>(n+1,10001)));
    while(m--)
    {
        cin>>s>>t>>val;
        grid[s][t][0] = val;
        grid[t][s][0] = val;
    }

    for(int k=1;k<n+1;k++)
    {
        for(int i=1;i<n+1;i++)
        {
            for(int j=1;j<n+1;j++)
            {
                grid[i][j][k] = min(grid[i][j][k-1],grid[i][k][k-1]+grid[k][j][k-1]);
            }
        }
    }
    
    int q;
    cin>>q;
    int src,des;
    while(q--)
    {
        cin>>src>>des;
        if(grid[src][des][n]>10000) cout<<-1<<endl;
        else cout<<grid[src][des][n]<<endl;
    }
}
~~~

如下是使用二维grid数组的代码：

~~~
#include<iostream>
using namespace std;
#include<vector>
#include<climits>

int main()
{
    int n,m;
    cin>>n>>m;
    int s,t,val;
    vector<vector<int>> grid(n+1,vector<int>(n+1,10001));
    while(m--)
    {
        cin>>s>>t>>val;
        grid[s][t] = val;
        grid[t][s] = val;
    }

    for(int k=1;k<n+1;k++)
    {
        for(int i=1;i<n+1;i++)
        {
            for(int j=1;j<n+1;j++)
            {
                grid[i][j] = min(grid[i][j],grid[i][k]+grid[k][j]);
            }
        }
    }
    
    int q;
    cin>>q;
    int src,des;
    while(q--)
    {
        cin>>src>>des;
        if(grid[src][des]>10000) cout<<-1<<endl;
        else cout<<grid[src][des]<<endl;
    }
}
~~~

