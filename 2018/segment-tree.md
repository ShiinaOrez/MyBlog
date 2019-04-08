---
title: Segment-Tree
date: 2018-03-31 16:37:01
tags: algorithm
---

#  线段树--动态区间查询

## 前言：
 现在手上有一个数组a[n]，让我们来求一个区间的和
 这简直太简单了！
 我们写一个前缀和就可以在O（1）解决问题了！

 问题升级：
 有两种操作，一个是修改其中某个变量的值，
 另一个是求区间和

 这。。我只能O（n²）去做了==

 别怕， 线段树可以解决你的问题。

# 线段树--Segment Tree
 既然是线段树，那么一定具有一个树的结构。
 这个树和其他树不一样，线段树是用来**维护**我们手中的数组的

 树的结点有这么几个属性：l,r,w
 l代表left，是这个结点对应区间的左端点
 r代表right，是这个结点对应区间的右端点
 w代表这个区间内的区间和
 
```
struct node{
    int l;
    int r;
    int w;
}tree[maxn];
```
 那我们来想一下大致的过程吧：
 首先我们要对于初始的数组a[n]来把这个线段树建立起来
 然后，如果要修改一个值，我们就要修改对应叶子结点和它所有的父节点
 查找区间和emmm
 把一个大区间分成许多我们维护的小区间就好了~

 ~~看上去挺简单的~~

## 建树--build_tree()
 建树也好，建堆也好，这种操作我们经常使用，就是初始化嘛~
 我们从根节点开始初始化，把区间逐渐二分下去。
 

```
void build_tree(int x,int left,int right){//x是当前节点标号
    tree[x].l=left;
    tree[x].r=right;
    if(left==right){
        change(x,a[left]);//修改x的值为a[left]
        return ;
    }
    build_tree(2*x,left,(left+right)/2);
    build_tree(2*x+1,(left+right)/2+1,right);
    //左右分别递归调用
    return ;
}
int main(){
	/**/
	build_tree(1,1,n);
	/**/
} 
```
 这样我们就能递归的建立一个线段树了。
 
## 修改--change()
 ~~刚刚的代码里出现了什么奇怪的东西！~~
 是的，我们调用了还没写的change函数
 change，就是把一个节点的值进行修改（现在我们简单的定义为加法）
 获取的参数是结点的序号，还有改动的值
 然后递归向上修改所有的父亲节点：
 

```
void change(int x,int a){//x节点的值修改为tree[x].w+a
    if(x==0) return ;//代表更新完毕
    tree[x].w+=a;
    change(x/2,a);
    return ;
}
```
## 查询--find()
 那么，我们拿到的指令是，修改a[i]
 怎么通过a[i]找到对应的那个叶子结点呢？
 二分嘛：
 

```
int find(int x,int p,int left,int right){//返回a[x]对应的线段树中的p
    if(left==right) return p;//找到了
    p=(x<=(left+right)/2):p*2?p*2+1;//x在左边，就找左儿子，在右边就找右儿子
    return find(x,p,tree[p].l,tree[p].r);//接着找
}
```
## 返回区间和--add()
 我们已经可以修改并且维护这颗大树了！
 接下来只要算算区间和究竟是多少就好了

 我们拿到一个区间，这个区间很大可能没有直接与之对应的节点
 往往可能是5个，6个节点拼凑出要求的区间

 写几个判断语句，然后递归：
 

```
int add(int x,int left,int right){//返回区间和
    if((tree[x].l==left)&&(tree[x].r==right))//正好相等，返回w
        return tree[x].w;
    int mid=(tree[x].l+tree[x].r)/2;
    if(right<=mid) //只可能存在于左儿子
        return add(x*2,left,right);
    if(left>mid) //只可能存在于右儿子
        return add(x*2+1,left,right);
    return add(x*2,left,mid)+add(x*2+1,mid+1,right);//最普遍情况，再次一分为二
}
```

 调用的时候，x为1，因为要从根节点开始寻找

 这样我们就可以进行修改和在线查询了。
  前缀和求区间和的方法我们称为离线算法。
  而今天的线段树是在线算法。
  不同的情况，对于在线离线的要求是不一样的。

#  **~~当然，能写前缀和我绝对不写线段树~~**
 
 ------
 最后祝各位OIer武运昌隆！！！

 ![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/NiLoyt5AhXAz2zmK0iJj3i4hz0wRXkVlo3gQfpTD5*Q!/r/dPMAAAAAAAAA)
