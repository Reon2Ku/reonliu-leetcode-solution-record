# A*算法精讲

## 题目描述

骑士的攻击

###### 题目描述

在象棋中，马和象的移动规则分别是“马走日”和“象走田”。现给定骑士的起始坐标和目标坐标，要求根据骑士的移动规则，计算从起点到达目标点所需的最短步数。



棋盘大小 1000 x 1000（棋盘的 x 和 y 坐标均在 [1, 1000] 区间内，包含边界）

###### 输入描述

第一行包含一个整数 n，表示测试用例的数量，1 <= n <= 100。

接下来的 n 行，每行包含四个整数 a1, a2, b1, b2，分别表示骑士的起始位置 (a1, a2) 和目标位置 (b1, b2)。

###### 输出描述

输出共 n 行，每行输出一个整数，表示骑士从起点到目标点的最短路径长度。

###### 输入示例

```
6
5 2 5 4
1 1 2 2
1 1 8 8
1 1 8 7
2 1 3 3
4 6 4 6
```

###### 输出示例

```
2
4
6
5
1
0
```



## 题解

首先来看纯粹的广搜

（实在是惭愧，此前都没有正儿八经的使用过卡尔的广搜法）

然而纯广搜时间复杂度实在太大了，过不了用例。

~~~
#include<iostream>
#include<string.h>
#include<queue>
using namespace std;

int grid[1001][1001];
int dir[8][2] = {-2,-1,-2,1,-1,2,1,2,2,1,2,-1,1,-2,-1,-2};

void bfs(int a1,int a2,int b1,int b2)
{
    queue<int> que;
    que.push(a1);
    que.push(a2);
    while(!que.empty())
    {
        int m = que.front(); que.pop();
        int n = que.front(); que.pop();
        if(m == b1&&n == b2) break;
        for(int i=0;i<8;i++)
        {
            int mm = m+dir[i][0];
            int nn = n+dir[i][1];
            if(mm<1 || nn<1 || mm>1000 ||nn>1000) continue;
            if(!grid[mm][nn])
            {
                que.push(mm);
                que.push(nn);
                grid[mm][nn] = grid[m][n]+1;
            }
        }
    }
}

int main()
{
    int n,a1,a2,b1,b2;
    cin>>n;
    while(n--)
    {
        cin>>a1>>a2>>b1>>b2;
        memset(grid,0,sizeof(grid));//本行代码用于将grid中的元素全部置为0
        bfs(a1,a2,b1,b2);
        cout<<grid[b1][b2]<<endl;
    }
    return 0;
}
~~~

A*是一种改良版的广度优先搜索，只在队列中存入逐渐逼近终点的节点，避免存入了大量无关紧要的节点。

~~~
#include<iostream>
#include<string.h>
#include<queue>
using namespace std;

int grid[1001][1001];
int dir[8][2] = {-2,-1,-2,1,-1,2,1,2,2,1,2,-1,1,-2,-1,-2};

int b1,b2;
//F = G + H
//G = 起点到该节点的路径消耗
//H = 终点到该节点的路径消耗

struct Knight
{
    int x,y;
    int g,h,f;
    bool operator < (const Knight& k) const
    {
        return k.f<f;
        //重载<操作符的含义如下：
        //当k.f<this.f时，this<k
        //也就是说，f值较小的Knight节点优先级更高
    }
};

priority_queue<Knight> que;

int Huristic(const Knight& k)//计算欧氏距离
{
    return (k.x-b1)*(k.x-b1) + (k.y-b2)*(k.y-b2);
}

void astar(const Knight& k)//启发式函数，用于约束骑士永远向最逼近终点的方向移动
{
    Knight curr, next;
    que.push(k);
    while(!que.empty())
    {
        curr = que.top(); que.pop();
        if(curr.x==b1&&curr.y==b2) break;
        for(int i=0;i<8;i++)
        {
            next.x = curr.x+dir[i][0];
            next.y = curr.y+dir[i][1];
            if(next.x<1||next.y<1||next.x>1000||next.y>1000) continue;
            if(!grid[next.x][next.y])
            {
                grid[next.x][next.y] = grid[curr.x][curr.y]+1;
                next.g = curr.g+5;
                next.h = Huristic(next);
                next.f = next.g+next.h;
                que.push(next);

            }
        }
    }
}

int main()
{
    int n,a1,a2;
    cin>>n;
    while(n--)
    {
        cin>>a1>>a2>>b1>>b2;
        memset(grid,0,sizeof(grid));//本行代码用于将grid中的元素全部置为0
        Knight k;
        k.x = a1;
        k.y = a2;
        k.h = 0;
        k.g = Huristic(k);
        k.f = k.g;
        
        astar(k);
        while(!que.empty()) que.pop();
        cout<<grid[b1][b2]<<endl;
    }
    return 0;
}
~~~

