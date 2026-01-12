# Bellman_ford单元有限最短路

## 题目描述

城市间货物运输 III

###### 题目描述

某国为促进城市间经济交流，决定对货物运输提供补贴。共有 n 个编号为 1 到 n 的城市，通过道路网络连接，网络中的道路仅允许从某个城市单向通行到另一个城市，不能反向通行。



网络中的道路都有各自的运输成本和政府补贴，**道路的权值计算方式为：运输成本 - 政府补贴。**权值为正表示扣除了政府补贴后运输货物仍需支付的费用；权值为负则表示政府的补贴超过了支出的运输成本，实际表现为运输过程中还能赚取一定的收益。



请计算在最多经过 k 个城市的条件下，从城市 src 到城市 dst 的最低运输成本。

###### 输入描述

第一行包含两个正整数，第一个正整数 n 表示该国一共有 n 个城市，第二个整数 m 表示这些城市中共有 m 条道路。

接下来为 m 行，每行包括三个整数，s、t 和 v，表示 s 号城市运输货物到达 t 号城市，道路权值为 v。

最后一行包含三个正整数，src、dst、和 k，src 和 dst 为城市编号，从 src 到 dst 经过的城市数量限制。

###### 输出描述

输出一个整数，表示从城市 src 到城市 dst 的最低运输成本，如果无法在给定经过城市数量限制下找到从 src 到 dst 的路径，则输出 "unreachable"，表示不存在符合条件的运输方案。

###### 输入示例

```
6 7
1 2 1
2 4 -3
2 5 2
1 3 5
3 5 1
4 6 4
5 6 -2
2 6 1
```

###### 输出示例

```
0
```

###### 提示信息

从 2 -> 5 -> 6 中转一站，运输成本为 0。 

1 <= n <= 1000； 

1 <= m <= 10000; 

-100 <= v <= 100;



## 题解

nm自己写了大半天没搞出来，最后还是看了答案

~~~
#include<iostream>
using namespace std;
#include<vector>
#include<climits>

int main()
{
    int n,m;
    cin>>n>>m;
    vector<vector<int>> times(m+1,vector<int>(3,0));
    for(int i=1;i<=m;i++)
    {
        cin>>times[i][0]>>times[i][1]>>times[i][2];
    }
    int src,dst,k;
    cin>>src>>dst>>k;
    //vector<bool> visited(n+1,0);
    vector<int> minDist(n+1,INT_MAX);
    minDist[src] = 0;
    vector<int> temp(n+1);
    for(int i=0;i<k+1;i++)
    {
        temp = minDist;
        for(int j=1;j<=m;j++)
        {
            if(temp[times[j][0]]==INT_MAX) continue;
            minDist[times[j][1]] = min(minDist[times[j][1]],temp[times[j][0]]+times[j][2]);
        }
    }
    if(minDist[dst]==INT_MAX) cout<<"unreachable";
    else cout<<minDist[dst];
}
~~~

