---
title: Kruskal和Prim--最小生成树算法
date: 2018-03-31 17:03:29
tags: algorithm
---

# 最小生成树： #
最小生成树是指一个有 n 个结点的连通图的生成树是原图的极小连通子图，且包含原图中的所有 n 个结点，并且有保持图连通的最少的边。

## 稀疏图与稠密图的区别： ##

判断标准如下：

	假设一个图G=(V,E)有n个节点，图G的每个节点的出度是一个固定的常数：k。由于E=kV=O(V) ，所以我们把符合E=O(V) 条件的图称为稀疏图。
    同理 ：
    如果一个图G=(V,E)有n个节点，假设图G的每个节点的出度是关于n的一个小数，并且0<f<=1，我们把符合E=fV2(平方)=V2(平方)条件的图称为稠密图。
    比如：一个图节点为16，节点的出度为4，那么f = 0.25。
 ~~显然。。稠密图并不多见~~
## 稀疏图-->Kruskal算法
因为稀疏图中边的数量和点的数量是在同一数量级上，即O（n）级别。所以我们从边下手。
因为是最小生成树，所以我们先把边进行排序，然后从小到大扫描，建立一个新树。这个新的树连通所有的结点。

1.我们首先进行排序，对稳定性没有要求的情况下我们使用内建的sort就好了。。。

```C++
struct Node{
	int from;
	int to;
	int w;
}g[maxn];//我们使用结构体来储存边，具有三个属性，起点，终点还有权值
bool comp(const Node p1,const Node p2){
	return p1.w<p2.w;
}//comp函数，我们搞一个升序数列

sort(g+1,g+1+m,comp);
```
2.现在我们有了一个边的集合，它是有序的。
所以我们从小到大开始对边进行分析，这个时候使用并查集的思想。

	并查集：我们这里把所有的点看做是未连接的，即n个树构成的森林。
	这里每棵树都是独立的。
	我们如果发现这一条边两边的结点不属于同一棵树，就建立起这条边。
	边建立的同时，这两棵树变成了一棵树。

```C++ 
int c=0;//c是遍历边的一个变量 
for(int i=1;i<=m;i++){
		if(c==n-1) break;
		int pre=g[i].from;
		int ver=g[i].to;
		if(find(pre)!=find(ver)){//不属于同一棵树
			add(pre,ver,g[i].w);
			add(ver,pre,g[i].w);//无向图，所以添加两条边
			union_forst(pre,ver);//合并集合
			c++;
		}
	}
```

## 练习
CodeVS：1078最小生成树
代码：http://paste.ubuntu.com/26115611/
 
## 稠密图--> Prim算法
稠密图中对边分析显然是不对的，因为边的数量级远超点的数量级。
所以我们对点下手。

1.因为最终的生成树要包括所有的结点，所以我们任选一个结点作为起始起点。
2.把这个点加入生成树，所以此时生成树只有一个结点。
3.我们查找此时生成树对外的所有的边进行查找最小，然后加入
4.加入连接的另一个点
 
### 示例代码：

```
#include<cstdio>
#include<iostream>
#include<algorithm>
#define maxn 100005
using namespace std;
struct node{
	int next;
	int to;
	int w;
}edge[maxn];
struct Node{
	int from;
	int to;
	int w;
}g[maxn];
int n,m,e=0;
int fa[maxn],head[maxn];
void add(int u,int v,int d){
	edge[++e].next=head[u];
	edge[e].to=v;
	edge[e].w=d;
	head[u]=e;
	return ;
}
bool comp(const Node p1,const Node p2){
	return p1.w<p2.w;
}
void prim(){
	for(int i=1;i<n;i++){
		for(int i=1;i<=m;i++){
			int pre=g[i].from;
			int ver=g[i].to;
			if((fa[pre]==0&&fa[ver]!=0)||(fa[pre]!=0&&fa[ver]==0)){
				if(fa[pre]!=0)
			 	 fa[pre]=0;
				else fa[ver]=0;  
				add(pre,ver,g[i].w);
				add(ver,pre,g[i].w);
			}
		}
	}
	return ;
}
int main(){
	int u,v,d;
	scanf("%d%d",&n,&m);
	for(int i=1;i<m;i++){
		fa[i]=i;
		scanf("%d%d%d",&u,&v,&d);
		g[i].from=u;
		g[i].to=v;
		g[i].w=d;
	}
	sort(g+1,g+1+m,comp);
	prim();
	return 0; 
}
```

  这里prim就不细述了，大家根据我不专业的描述去读读看吧2333333

-----
就是这样
最后是惯例的祝各位OIer武运昌隆！

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/xD10jc4W8l7Z4lniZv6ab48fU4bOFzhuBfYyE47dl04!/r/dD4BAAAAAAAA)
