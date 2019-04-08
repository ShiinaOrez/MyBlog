---
title: 最近公共祖先LCA--Tarjan算法
date: 2018-03-31 17:20:46
tags: algorithm
---

# 最近公共祖先 Lowest Common Ancestors
 在一棵树中，两个结点之间第一个共同的祖先。
 如图：
 ![这里写图片描述](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1512556667109&di=7606e24595fe96661c90b18265edef83&imgtype=0&src=http://www.kuqin.com/upimg/allimg/140115/2252462243-0.jpg) 
 
 结点10和11的公共祖先有1、7、8、⑨四个节点，但是只有⑨是离其最近的，所以只有⑨是LCA
 同理，3和11的LCA即是根节点1。2和4的LCA是2。

# Tarjan算法：

 其实讲个道理，我也不知道究竟是不是tarjan算法，因为网上说这个dfs序离线做法不是tarjan啥的，姑且这么叫吧。。反正复杂度也是很低

## dfs序：
 作为OI利器大法师--dfs--深度优先搜索
 ~~据说是只要你dfs学得好，你就能一等，你就能拿金~~
 所谓dfs序，就是按照dfs遍历的顺序记录下点的顺序。
 再比如，还是上图为例：
 若是我们遍历的顺序是从左到右，则dfs序是这样的：
 
> *  1-2-3-2-4-5-4-6-4-2-1-7-8-9-10-9-11-9-8-7-1
 
 有这个实例在，应该可以理解了吧。
## 继续：
 既然提到了dfs序的概念，我想很多人已经知道接下来怎么求LCA了
 没错，就是在两个结点中找到深度最小的那个结点。

## 没错就是这么的。。简单

 没有想象中的复杂吧。。这就是算法的迷人之处呢。

 代码：
 

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
int c=0,lc,ans,e=0,q,n,m;
int find_p[maxn],dfs_d[maxn],dfs_p[maxn],dfs_w[maxn],head[maxn];
void add(int u,int v,int d){
	edge[++e].next=head[u];
	edge[e].to=v;
	edge[e].w=d;
	head[u]=e;
	return ;
}
void dfs_lca(int pre,int now,int deep,int weight){//遍历从第一个点开始，第一个输入的点-->根 
	c++;//遍历顺序编号 
	dfs_p[c]=now;//此节点的下标 
	dfs_d[c]=deep;//深度 
	dfs_w[c]=weight;//权值和-->沿路权值和 
	for(int i=head[now];i!=0;i=edge[i].next){//now相连的所有点 
		if(edge[i].to!=pre){//不向上 
			dfs_lca(now,edge[i].to,deep+1,weight+edge[i].w);//继续dfs 
			c++;//回到这个节点 
			dfs_p[c]=now;//同样记录 
			dfs_d[c]=deep;
			dfs_w[c]=weight;
		}
	}
	c++;//同样又一次回到了起点 
	dfs_p[c]=now;
	dfs_d[c]=deep;
	dfs_w[c]=weight;
	find_p[now]=c;//now这个点最后被记录的位置 
	return ;
}
void lca(){
	dfs_lca(0,1,1,0);
	return ;
}
void find_lca(int u,int v){
	if(find_p[u]>find_p[v]) swap(u,v);
	int ans=min(dfs_d[find_p[u]],dfs_d[find_p[v]]);
	for(int i=find_p[u];i<=find_p[v];i++){
		if(dfs_d[i]<ans) lc=dfs_p[i];
	}
	return ;
}
int main(){
	int u,v,d;
	scanf("%d",&m);
	for(int i=1;i<=m;i++){
		scanf("%d%d%d",&u,&v,&d);
		add(u,v,d);
		add(v,u,d);
	}
	lca();
	scanf("%d",&q);
	for(int i=1;i<=q;i++){
		scanf("%d%d",&u,&v);
		find_lca(u,v);
		printf("%d",lc);
	}
	return 0;
}
```

 代码中使用的数据结构是链表，但是是用数组模拟的办法，需要学习这种方法的同学可以去我的另一篇博客：

# http://blog.csdn.net/Shiina_Orez/article/details/78724156 -->数组模拟链表

-----
 祝各位OIer武运昌隆！！！
 ![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/C3pTelk8lgVsdohSiEpZ*stIEPgiI68MM9x7YG7Bh.s!/r/dPMAAAAAAAAA)
