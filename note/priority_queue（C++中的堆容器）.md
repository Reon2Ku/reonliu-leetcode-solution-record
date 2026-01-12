# priority_queue

C++ 中的 `priority_queue`（优先队列）是标准模板库（STL）提供的一种**容器适配器**。它默认情况下是一个**最大堆（Max-Heap）**，这意味着每次从队列中取出的元素总是当前队列中**优先级最高**的元素（默认是最大值）

优先队列的声明：

~~~
priority_queue<int> pq;
~~~

默认情况下，这是一个大顶堆。

当我希望重写优先队列的排序逻辑时（如想要使用小顶堆）我们通常这样写：

~~~
class/struct mycomparision
{
	public:
	bool operator(const int& a,const int& b)
	//注意此处可以写作bool operator(int a,int b)
	//加入const &是一种良好的编程习惯，既保证源数据不会被修改（const），又避免了不必要的参数拷贝(&)
	{
		return a>b;
		//当a>b时，a的优先级比b低。这是小顶堆逻辑
		//同样的，如果是return a<b那就是大顶堆
		//当a<b时，a的优先级比b低
	}
};

priority_queue<int,vector<int>,mycomparision> pq;
//特别注意此处在声明时，如果想要自行指定比较函数，那就要先指定需要使用的存储容器。priority_queue并非某种特定的容器，而是在queue或者vector数据结构的基础上做大顶堆的维护。由于C++中指定参数时顺序是固定的，没办法跳过容器的指定而直接指定比较函数。因此需要加入vector<int>用于指定存储int数据的底层结构。
~~~

在处理**Dijkstra算法**问题时，有一个经典的对于小顶堆的应用

~~~
//小顶堆
class mycomparision
{
    public:
    bool operator()(const pair<int,int>& lhs,const pair<int,int>& rhs)
    {
        return lhs.second>rhs.second;
    }
};
//优先队列中存放pair<节点编号，原点到该节点的权值>
priority_queue<pair<int,int>,vector<pair<int,int>>,mycomparision> pq;
~~~

希望你能从这种写法中得到某些启示
