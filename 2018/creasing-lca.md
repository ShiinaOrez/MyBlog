---
title: 树上倍增以及LCA问题
date: 2018-05-08 12:50:07
tags: algorithm 
---
## 前言：
	因为HUST校赛所以就要复习一下以前学过的知识，很多以前学过的知识全都忘光了，就很难受，所以慢慢的整理一下；

## 正题：树上倍增以及lca问题

什么是lca？
最近公共祖先（Lowest Common Ancestors）
至于定义是什么我不想废话，自行百度吧23333

关于求lca，最暴力的办法就是dfs一遍然后找两个节点之间最高的点；
就像两个点一个个地往中间跳一样。

一个，一个

好慢啊

所以我们可不可以一下子跳好多呢？

	很明显：对于C=二进制下所有1对应的2的幂的和

所以我们完全可以靠跳若干次的2^i来到达lca嘛！

### step1：

首先我们要做的就是平衡两个点的深度（高度）；
因为**公共**祖先嘛，大家一起跳才比较整齐[bushi

大概就是，找出比较深的那个节点，然后通过跳若干次到达和另一节点同样高度的结点；至于深度在搜索的时候可以顺便拿到。

### step2：

跳，怎么跳？

我们通过构造一个转移状态数组来保存一个结点 i 向上跳 2^j 个结点的结果

	father[i][j]=father[father[i][j-1]][j-1]

### step3:

跳，但是注意。
如果两个节点跳之后相同，那么就不跳。
这样最后你会到达lca的前一个点。
最后再跳一下就好了23333。

###source code：

```C
#include<cstdio>
#include<cmath>
#define maxn 1005
using namespace std;

struct node{
	int next;
	int to;
}edge[maxn];

int head[maxn],e,n;

int d[maxn],f[maxn][10];
int deepest=0;
void add(int u,int v){
	edge[++e].next=head[u];
	edge[e].to=v;
	head[u]=e;
	return ;
}

void init_0(int now,int from){
	d[now]=d[from]+1;
	f[now][0]=from;
	for(int i=head[now];i!=0;i=edge[i].next){
		if(edge[i].to!=from)
			init_0(edge[i].to,now);
	}
	if(d[now]>deepest) deepest=d[now];
	return ;
}

void init_all(){
	for(int i=1;i<=log2(deepest);i++){
		for(int i=1;i<=n;i++){
			f[n][i]=f[f[n][i-1]][i-1];
		}
	}
	return ;
}

int get_too(int a,int b){
	int c=d[a]-d[b],step=0;
	while(c>0){
		if(c&1){
			a=f[a][step];
			c>>1;
		}
		step++;
	}
	return a;
}

int jump(int a,int b){
	for(int i=log2(deepest);i>=0;i--){
		if(f[a][i]==f[b][i]) continue;
		a=f[a][i];
		b=f[b][i];
	}
	return f[a][0];
}

int main(){
	int a,b;
	scanf("%d",&n);
	for(int i=1;i<n;i++){
		int u,v;
		scanf("%d %d",&u,&v);
		add(u,v);add(v,u);
	}
	d[1]=1;
	init_0(1,0);
	init_all();
	scanf("%d %d",&a,&b);
	if(d[a]<d[b]){
		int swap=a;a=b;b=swap;
	}
	a=get_too(a,b);
	int ans=jump(a,b);
	printf("%d",ans);
	return 0;
}
```

写的不好看，见谅

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/cNpJj*gIQdtcTK1Atig87QNNKCCmuIJ7S6RmB9lo8I8!/r/dFYBAAAAAAAA)
