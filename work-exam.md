2012-网易-校园招聘-C++开发工程师
==================================

Fibonacci number
------------------

F(n)的值是多少？

![Fabonacci](https://lh6.googleusercontent.com/-0n60YMZUq5c/UHZZgx84uQI/AAAAAAAABHQ/hbn9epSpIaI/s174/NumberedEquation6.gif)

**常规算法**：根据Fabonacci的定义，递归求值。时间复杂度O(2^n)

	def fibobacci(n):
		return n>=2 and fibonacci(n-2) + fibonacci(n-1) or n

**迭代**：利用循环取代递归。时间复杂度O(n)

	def fibonacci(n):
		a, b = 0, 1
		for i in range(n):
			a, b = b, a+b
		return a

*迭代算法的变种* ：利用python的generator，原理一样，只是应用场合不一样。

	def fibonacci():
		a, b = 0, 1
		while 1:
			yield a
			a, b = b, a+b
	
	f = fibonacci()
	n = 100
	for i in range(n+1):
		print(next(f)

**利用矩阵运算来加速的算法**：（还没懂）算法复杂度O(logn)

	import numpy

	fibonacci_matrix = numpy.matrix([1,1],[1,0], dtype=numpy.ndarray)
	def fibonacci(n):
		return fibonacci_matrix**(n-1)[0, 0]

**数学方法:使用Fibonacci序列的通用公式**：

	from math import sqrt

	def fibonacci(n):
		return int(((1+sqrt(5))/2)**n/sqrt(5))

虽然数学方法在4种方法中速度最快，但当n越大，其计算精度偏差越大。

图的遍历
----------

![graph](https://lh3.googleusercontent.com/-WAC-FEacK3A/UHZ1eVyHU-I/AAAAAAAABHk/pHRhHRwszLQ/s347/graph-sample.jpg)

	#include <iostream>
	#include <queue>
	#include <stack>
	#include <stdio.h>
	#include <stdlib.h>
	#define MAX 100
	using namespace std;
	
	typedef struct 
	{
		int edges[MAX][MAX];	//邻接矩阵
		int n;					//顶点数
		int e;					//边数
	}MGraph;
	
	bool visited[MAX];			//标记顶点是否被访问过
	
	void createMGraph(MGraph &G)	//用引用作参数
	{
		int i, j;
		int s, t;					//存储顶点编号
		int v;						//存储边的权值
		for(i=0; i<G.n; i++)		//初始化
		{
			for(j=0; j<G.n; j++)
			{
				G.edges[i][j] = 0;
			}
			visited[i]=false;
		}
		for(i=0; i<G.e; i++)		//对矩阵相邻的边赋权值
		{
			scanf("%d %d %d", &s, &t, &v);		//对输入边的顶点编号以及权值
			G.edges[s][t] = v;
		}
	}
	
	void DFS(MGraph G, int v)		//深度优先搜索
	{
		int i;
		printf("%d ", v);			//访问结点v
		visited[v]=true;
		for(i=0; i<G.n; i++)		//访问与v相邻的未被访问过的结点
		{
			if(G.edges[v][i]!=0 && visited[i]==false)
			{
				DFS(G,i);
			}
		}
	}
	
	void DFS1(MGraph G, int v)		//非递归实现
	{
		stack<int> s;
		printf("%d ", v);			//访问初始结点
		visited[v]=true;
		s.push(v);					//入栈
		while(!s.empty())
		{
			int i, j;
			i = s.top();			//取栈顶元素
			for(j=0; j<G.n; j++)	//访问与顶点i相邻的顶点
			{
				if(G.edges[i][j]!=0 && visited[j]==false)
				{
					printf("%d ", j);	//访问
					visited[j]=true;
					s.push(j);			//访问完后入栈
					break;				//找到一个相邻未访问的顶点，访问之后则跳出循环
				}
			}
			if(j==G.n)				//如果与i相邻的顶点都被访问过，则将顶点i出栈
				s.pop();
		}
	}
	
	void BFS(MGraph G, int v)		//广度优先搜索
	{
		queue<int> Q;
		printf("%d ", v);
		Q.push(v);
		while(!Q.empty())
		{
			int i, j;
			i = Q.front();		//取队首顶点
			Q.pop();
			for(j=0; j<G.n; j++)	//广度遍历
			{
				if(G.edges[i][j]!=0 && visited[j]==false)
				{
					printf("%d ", j);
					visited[j]=true;
					Q.push(j);
				}
			}
		}
	}
	
	int main(void)
	{
		int n, e;		//建立的图的顶点数和边数
		while(scanf("%d %d", &n, &e)==2 && n>0)
		{
			MGraph G;
			G.n = n;
			G.e = e;
			createMGraph(G);
			DFS(G, 0);
			printf("\n");
			//DFS1(G, 0);
			//printf("\n");
			//BFS(G, 0);
			//printf("\n");
		}
		return 0;
	}


大文件处理
------------

设计算法从文件中随机取K行(内存够存储K行)，每行被取的概率相等，算法复杂度要低。(具体的记不太清楚了)

CAS(Compare And Swap)
-------------------------

	bool compare_and_swap(int *accum, int *dest, int newval)
	{
		if(*accum == *dest)
		{
			*dest = newval;
			return true;
		}
		return false;
	}

CAS方式实现的进队列操作：

	EnQueue(x)	//进队列
	{
		q = new record();
		q->value = x;
		q->next = NULL;

		do {
			p = tail;	//取链表尾指针的快照
		}while(CAS(p->next, NULL, q) != TRUE)	//如果没有把结点链上，再试

		CAS(tail, p, q);	//置尾结点
	}

1. [无锁的数据结构（Lock-Free）及CAS（Compare-and-Swap）机制](http://blog.csdn.net/lifesider/article/details/6582338)

2. [Wikipedia---Compare-and-swap](http://en.wikipedia.org/wiki/Compare-and-swap)

3. [无锁队列的实现](http://coolshell.cn/articles/8239.html)


排序算法
----------

不稳定排序算法可能会在相等的键值中改变记录的相对次序，但是稳定排序算法从来不会如此。

**稳定的**

- 冒泡排序(Bubble sort) --- O(n^2)

- 插入排序(Insertion sort) --- O(n^2)

- 桶排序(Bucket sort) --- O(n)，需要O(k)额外空间

- 计数排序(Counting sort) --- O(n+k)，需要O(n+k)额外空间

- 归并排序(Merge sort) --- O(nlogn)，需要O(n)额外空间

- 基数排序(Radix sort) --- O(n * k)，需要O(n)额外空间

**不稳定的**

- 选择排序(Selection sort) --- O(n^2)

- 希尔排序(Shell sort) --- O(nlogn)

- 堆排序(Heap sort) --- O(nlogn)

- 快速排序(Quick sort) --- O(nlogn)


**代码实现**

*冒泡排序---Python*:

	def bubbleSort(L):
		print 'start bubble sort:'
		print L
		count = 0
		while True:
			step = 0
			for i in range(len(L)-1):
				if L[i] > L[i+1]:
					L[i], L[i+1] = L[i+1], L[i]
					print L
					step += 1
					count += 1
			if step == 0:
				return L, 'totalstep:', count

*计数排序---C*:

	
	#include <stdlib.h>
	
	void counting_sort(int *array, int size)
	{
		int i, min, max;
		min = max = array[0];
		for(i=1; i<size; i++)
		{
			if(array[i] < min)
				min = array[i];
			else if(array[i] > max)
				max = array[i];
		}

		int range = max-min+1;
		int *count = (int *)malloc(range * sizeof(int));

		for(i = 0; i < range; i++)
			count[i] = 0;
		for(i = 0; i < size; i++)
			count[array[i]-min]++;

		int j, z = 0;
		for(i=min; i<=max; i++)
			for(j=0; j<count[i-min]; j++)
				array[z++] = i;
		free(count);
	}

*快速排序---Python*:

	def qsort(L):
		if not L: return []
		return qsort([x for x in L[1:] if x<L[0]]) + L[0:1] + \
			qsort([x for x in L[1:] if x>=L[0]])