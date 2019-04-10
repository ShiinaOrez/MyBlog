---
title: Union_find
date: 2018-03-31 16:41:30
tags: algorithm
---

# 并查集--并、查、集。

 并查集这个名字听上去是不是怪怪的？？
 对的，应该是怪怪的才对！
 其实是三个汉字拼凑而成的术语23333
## 并--Union：
 并，指的是合并操作。
 即将两个集合合并为一个集合。
## 查--Find：
 查，指的是查询。
 查询一个节点的所属集合。
## 集：
 集，指的是集合。

 这样几个概念为什么能够拼凑在一起呢？
 我们从并查集最经典的亲戚问题入手：

	如果你面对一个很大的调查人群，每个人都会告诉你一个亲戚关系。
	你怎么才能知道，那几个人是一个家族？或者一共有几个家族？？？

 我们分析一下问题，亲戚群落可以看成是一个集合。彼此是亲戚的人肯定在同一个集合。如果两个不同集合的人之间有亲戚关系，那么这两个集合应该合并为一个才对吧！

 事实上，我们这么一分析，并查集就全部出来了，但是“查”这个概念是隐形的，它存在于怎么判断两个是亲戚的人存在于同一集合还是存在于不同集合。

 那我们就从查讲起：
## 查询是否同一集合
 思想十分简单：
	 我们可以指定一个人作为家族的代表嘛！（甚至这个人可以是刚刚满月的宝宝！）
	 我们设置一个数组father，这个数组指向一个人的所属家族的另一个人。
	 比如我们知道a和b是属于同一个家族的，那我们就把father[a]=b;
	 这样我们就可以把所有人串在一根线上，只要向上寻找，就能找到家族的代表人物。
	 怎么判断代表人物是谁啊？因为代表人物的father指向它本身。
	 所以一个while循环就可以得出一个人所在家族的代表人物，
	 如若两个人得出的代表人物不一致，那么是不同家族。

## 合并集合
	突然发现两个不同家族的人是亲戚诶。。那么这两个家族中的任意两人都是亲戚了！
	所以我们合并一下集合。
	如何操作呢？
	只要把其中一个代表人物指向另一个代表人物就好啦。。。

 
##代码：

 

```
#include<cstdio>
#include<algorithm>
#include<iostream>
#define maxn 100005
using namespace std;
int fa[maxn];
int n,m,q;
int find(int x){
	int y=x;
	if(fa[y]!=y)
	  fa[x]=find(fa[y]);//这里要注意，这里可以减少大量复杂度
	else return y;
	return fa[x];  
}
void union(int u,int v){
	fa[find(u)]=v;
	return ;
}
int main(){
	int u,v;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++)
	  fa[i]=i;
	for(int i=1;i<=m;i++){
		scanf("%d%d",&u,&v);
		if(find(v)!=find(u))
		  union(u,v);
	}
}
```
 这就是并查集。
 相比于算法，并查集更像是一种思想，应用范围十分广泛。以图论居多哦。

-----
 最后祝各位OIer武运昌隆，早日捧杯！

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/7t1ljDkdWvpG3ZJcz8m21VQ1UZ6Bb.wy80v.cQC4eZI!/r/dD4BAAAAAAAA)
 
 
 
 
 
   