---
title: 拓扑排序-topological-sort
date: 2018-04-24 21:33:21
tags: algorithm
---
## 什么是拓扑排序

	对一个有向无环图(Directed Acyclic Graph简称DAG)G进行拓扑排序，是将G中所有顶点排成一个线性序列，使得图中任意一对顶点u和v，若边(u,v)∈E(G)，则u在线性序列中出现在v之前。通常，这样的线性序列称为满足拓扑次序(Topological Order)的序列，简称拓扑序列。简单的说，由某个集合上的一个偏序得到该集合上的一个全序，这个操作称之为拓扑排序。

↑听不懂听不懂= =

随便刷刷编程题的人应该知道一个先行课问题，就是在大学中，选修一门课程是有它的固定的先行课的，只有完成了先行课才能选择对应的课程。

拓扑排序就是解决这个问题的。

## 如何实现？

我们就用先行课问题来进行讲解。

如果一门课程没有先行课，那么这个课程是基础课程，可以直接上的。而在我们上完所有的基础课程后，那些仅由基础课程为先行课的课程也相应解锁，以此类推。

具体实现为：
	> * 记录每个结点的入度和出度
	> * 建立一个队列，将所有的入度为0的点加入队列（因为是有向无环图所以一定存在入度为0的点）
	> * 在队列非空前，不断取出元素，遍历它连接的所有点，并将他们的入度-1，（删除该节点），如果有点入度为0，那么加入队列。
	> * OK

然后我们得到的序列中，满足所有的关系X->Y，都有
		
		index[x]<index[y] 

我们便完成了拓扑排序

## 源码（自己写的勿喷）

``` cpp
#include<cstdio>
#include<queue>
#define maxn 1005
using namespace std;
struct node{
	int next;
	int to;
}edge[maxn];
bool visit[maxn];
int e,head[maxn];
int in[maxn];
int n,m;
queue<int> list;
void add(int u,int v){
	edge[++e].next=head[u];
	edge[e].to=v;
	head[u]=e;
	return ;
}
void scan(int x){
	for(int i=head[x];i!=0;i=edge[i].next){
		int v=edge[i].to;
		in[v]--;
		if(!in[v]) list.push(v);
	}
	return ;
}
int main(){
	int u,v,w;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;i++){
		scanf("%d %d",&u,&v);
		add(u,v);
		in[v]++;
	}
	for(int i=1;i<=n;i++)
		if(!in[i]) list.push(i);
	while(!list.empty()){
		int now=list.front();
		scan(now);
		printf("%d ",now);
		list.pop();
	}
	return 0;
}
```
-----

欢迎大家到[ShiinaOrez的博客](https://blog.csdn.net/Shiina_Orez)

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/jGkqTLPCfDQJUA47vTYTfaotZSOPunLE.UBloy6Fyp8!/r/dGYBAAAAAAAA)
