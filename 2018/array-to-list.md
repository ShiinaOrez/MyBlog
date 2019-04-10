---
title: 数组模拟链表
date: 2018-03-31 21:29:40
tags: algorithm
---

链表作为一种数据结构，对于空间的利用率非常之高。但是对于链表没有学好的同学来说，链表难上天了。有没有一种解决方案能实现链表却又不那么难呢？

当然有：那就是：
# 数组模拟链表
顾名思义，通过一些特殊的方法，可以让数组具有链表的作用。

我们先放代码，然后解释原理。
 

```
int head[maxn],e=0; 
struct node{
	int next;
	int to;
	int w;
}edge[maxn];
void add(int u,int v,int d){
	edge[++e].next=head[u];
	edge[e].to=v;
	edge[e].w=d;
	head[u]=e;
	return ;
}
```
## Node：
其实我们一步步的看代码就能发现其中的端倪，究竟是怎么实现这么神奇的操作的。
最基本的元素显然是node，具有三个属性next，to和w。
**为什么没有from**？是因为链表都是单向的，没有回溯这一环。~~别和我说什么双向链表~~
 
## Add函数：
 add函数有三个参数：u，v，d。u、v代表两个点，d代表权值。
 函数的第一行涉及到了一个量：head[u]。于是我们寻找head[u]在哪里。
 我们在最后一行找到了，**head[u]被上一个函数赋值为e**。
 
 e是什么？
 大致看一眼就知道e代表边的总数，同时还是**每条边的序号**。
 因此head[u]代表上一个以u为起点的边的序号。
 在程序的一开始e便会++,所以e这个全局变量最终就是所有边的数量。
 
 再一次回到函数的开始， next属性赋值为head[u]。
 那个next就是指的上一个以u为起点的边的序号。

 到这里就看的很清楚了吧。我们也就知道届时该如何遍历这个链表。

```
for(int i=head[p];i!=0;i=edge[i].next){
	/*...*/
}
```

 
  这个循环的意义也就明白了，为什么是i！=0为判断条件，因为都是指向上一个以本节点为起点的边，那么第一条边之前没有边存在，那么就是0。


-----
就是这样，数组模拟链表的知识讲完了。
 祝各位OIer武运昌隆！！！


![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/gfHYaNBYZ5fuk5qdR5D4Wniy2t*K3UN368bij4JY.Uc!/r/dPMAAAAAAAAA)