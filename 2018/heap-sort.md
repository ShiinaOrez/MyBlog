---
title: Heap-Sort
date: 2018-03-31 17:01:52
tags: algorithm
---

# 堆-Heap（本文特指二叉堆）
 堆在算法界是一个非常常见的东西23333

 堆的优点在于它的复杂度，都控制到了O（log 2 n）的级别

## 堆的定义：
 堆是一个完全二叉树。既去掉最后一行 ，是一棵满二叉树。
 堆分为大根堆和小根堆。
 大根堆：除根节点外的每一个结点的父亲节点都要不小于结点本身。
 小根堆：除根节点之外的每一个结点的父亲结点都要不大于结点本身。
 
## 构造一个堆：
 就像是 一个二叉树一样，我们可以用一个一维数组去存储一棵树。
 同理，我们也可以用一个一维数组去存储一个堆。
 对于a[i].
 它的父亲是a[i/2];左儿子和右儿子分别是：a[i*2];a[i*2+1]
 所以我们把n个元素按照堆的性质进行排列，就能得到一个堆，这个过程成为build_heap()

### 具体思路：
	每个节点加入到堆的最后，然后进行上浮操作（假定我们要一个小根堆）：
		和自己的父亲节点进行比较，若是a[i]<a[i/2]；那么swap(i,i/2);
		然后再对i/2进行上浮操作；
	每个元素都这么做，最后我们就得到了一个小根堆。

 代码：
 

```
void build_heap(int a,int p){
	heap[p]=a;
	while(p>=1&&heap[p/2]>heap[p]){
		swap(heap[p/2],heap[p]);
		p/=2;
	}
	return ;
}

for(int i=1;i<=n;i++){
	scanf("%d",&a);
	build_heap(a,i);
}
```
## 堆排序--Heap_sort
 堆有一个特点，那就是根节点一定是堆中的最值。
 所以我们可以利用这一点写一个排序**（同样以小根堆为例子）**。

	每次，我们取出根节点。然后将堆末尾的元素赋值给根节点。
		//这一步代表了彻底取出根节点的元素，并且剩余元素的个数-1了。
	然后对根节点进行下沉操作：
		如果这个节点大于任何一个左儿子或者右儿子，那么交换。如果都大于，那么和小的那个儿子节点进行交换。
	循环n-1次之后，堆只剩一个元素，我们再取出，堆排序完成。
	显而易见复杂度是O（n log n）

 代码 ：
 

```
void heaply(int p){
	int l=p*2,r=p*2+1;
	if(heap[l]>heap[r]){
		swap(heap[r],heap[p]);
		heaply(r);
	}
	else{
		swap(heap[l],heap[p]);
		heaply(l);
	}
	return ;
}
void heap_sort(){
	for(int i=n;i>=1;i--){
		printf("%d ",heap[1]);
		heap[1]=heap[i];
		heaply(1);
	}
	return ;
}
```

------
### 堆排序只是堆的一种应用方式，堆作为一种结构。用途是很多的。
 
 最后当然还是祝各位OIer武运昌隆！！！

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/UgJT0EuSJGAMzBO6T2c0VIoCDahUgPl9Zi4wOa9*gH0!/r/dPMAAAAAAAAA)
 
  
