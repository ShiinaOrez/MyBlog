---
title: 强连通分量的tarjan算法
date: 2018-04-22 22:35:15
tags: algorithm
---
# strongly-connected-component
强连通分量是什么？
[百度百科比我讲的清楚：](https://baike.baidu.com/item/%E5%BC%BA%E8%BF%9E%E9%80%9A%E5%88%86%E9%87%8F/7448759?fr=aladdin)

----我真是无耻

-----
# 算法老神仙tarjan的tarjan算法：

tarjan算法是基于有向图的深度优先搜索树的一种算法；
在深度优先搜索的时候依赖index和low来找出环，从而求解强连通分量；

什么是index和low呢？别急：

## index
顾名思义，index是索引的意思。它用于存储一个点在深度优先搜索中的顺序。这个值只会被修改一次，也就是初始化的时候。
## low
用于记录一个结点及其子树能够到达的最小的index

显而易见，low[u]有这么一个公式

```C
	low[u]=min(index[v],low[u],low[v]);//其中v为u的子结点
```

那么为什么要这么做呢 ？其实我们很容易就可以发现。当搜索结束时，一个low[x]=index[x]的结点，u和它的子树（或者子树的一支）构成了一个强连通分量。因为在子树中到达了u本身。因此构成了一个有向环。

那么，很多人就会有一个问题，一个结点对应的子树不该只有一条链，而一个有向环肯定是由其中的一条组成的，如何保证不牵扯其他子链呢？

使用栈；
## 栈 stack
我拒绝手把手教你使用栈

在dfs时，把每次到达的结点压栈，判定index[u]==low[u]时，我们记录或者输出这个强连通分量，便不断地弹出栈中点，直至到u。这样可以保证子链不互相干扰。

## 实现：

``` cpp
#include<cstdio>
#include<vector>
#include<stack>
#define maxn 1005
using namespace std;
int e,tag;
bool stack_exist[maxn];//栈中是否存在该元素
int head[maxn],visit[maxn],index[maxn],low[maxn];//visit用于防止森林中不同的深度优先搜索树干扰
stack<int> s;
int n,m;
struct node{
	int next;
	int to;
	int w;
}edge[maxn];
void add(int u,int v,int w){
	edge[++e].next=head[u];
	edge[e].to=v;
	edge[e].w=w;
	head[u]=e;
	return ;
}
void tarjan(int x){
	index[x]=++tag;
	low[x]=index[x];
	visit[x]=1;
	s.push(x);
	stack_exist[x]=true;
	for(int i=head[x];i!=0;i=edge[i].next){
		if(visit[edge[i].to]==0){
			tarjan(edge[i].to);
			low[x]=min(low[x],low[edge[i].to]);
		}
		else if(stack_exist[edge[i].to]){
			if(visit[edge[i].to]!=2)
				low[x]=min(low[x],index[edge[i].to]);
		}
	}
	if(index[x]==low[x]){
		while(s.top()!=x){
			int v=s.top();
			s.pop();
			stack_exist[v]=false;
			visit[v]++;
			printf("%d ",v);
		}
		printf("%d\n",s.top());
		s.pop();
		visit[x]++;
	}
	return ;
}
int main(){
	int u,v,w;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;i++){
		scanf("%d %d %d",&u,&v,&w);
		add(u,v,w);
	}
	for(int i=1;i<=n;i++)
		if(visit[i]==0){
			printf("strongly connected components:");
			tarjan(i);
/*			for(int j=1;j<=n;j++){
				printf("%d ",visit[j]); 
			}
			printf("\n");
			printf("\n%d: %d %d\n",i,index[i],low[i]);*/
		}
	return 0;
}
```
就这样www

-----
欢迎大家到我的博客：https://blog.csdn.net/shiina_orez

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/JGCqBgWbPNPXSPEXa0dfiqW.3EhG4l41*P60CvAmy0A!/r/dDMBAAAAAAAA)


