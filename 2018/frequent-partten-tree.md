---
title: 基于关联规则的推荐-获取频繁项集
date: 2018-03-31 16:27:54
tags: algorithm
---

## 关联规则：
	关联规则是形如X->Y的蕴含表达式,其中X和Y是不相交的项集,即X∩Y=∅。

	关联规则的强度可以用它的支持度(support)和置信度(confidence)来度量

	支持度确定规则可以用于给定数据集的频繁程度（能够关联的次数）
	置信度确定Y在包含X的交易中出现的频繁程度（能够成功关联的次数）

		支持度s和置信度c的形式定义如下：

			||--> ·s(X->Y)=σ(X∪Y)/N
			||--> ·c(X->Y)=σ(X∪Y)/σ(X)

			因此，大多数关联规则挖掘算法通常采用的一种策略是：
**将关联规则挖掘任务分解为如下两个主要的子任务：**

1.频繁项集的产生：

		其目标是发现满足最小值尺度阈值的所有项集，这些项集称作频繁项集(frequent itemset)

2.规则的产生：

		其目标是从上一步发现的频繁项集中提取所有高置信度的规则，这些规则称作强规则(strong rule)


  **显而易见。频繁项集的产生所需要的计算开销远大于产生规则所需的计算开销。**

## 使用Apriori算法获取频繁项集
### 暴力挖取：
	最容易想到的、也是最直接的关联关系挖掘的方法或许就是暴力搜索	  (Brute-force)的方法。

	但是暴力的计算量过大，一个包含k个项的数据集可能产生2^k-1个	  频繁项集。

	发现频繁项集的一种原始方法是确定每一个候选项集(candidate -itemset)的支持度计数。为了完成这一任务，必须将每个候选项集与每个交易进行比较。

	如果候选项集包含在交易中则候选集的支持度计数增加。

复杂度O(NMω),N是交易数,M=2^k-1是候选项集数，而ω是交易的最大宽度(也就是交易中最大的项数)

### 先验原理：
		对于本身复杂度极高的Brute-force，我们必须设法降低产生频繁项集的计算复杂度。此时我们可以利用支持度对候选项集进行剪枝。

Apriori定律1：

	如果一个集合是频繁项集，则它所有的子集都是频繁项集。

Apriori定律2：

	如果一个集合不是频繁项集，则它的所有的超集都不是频繁项集。

剪枝原则：

	依据apriori定律2，当一个集合不是频繁项集时，剪除所有向下的集合。

频繁项集的产生：

R.Agrawal 和 R. Srikant于1994年在文献中提出了Apriori算法，该算法的描述如下：

```
·Let k=1
		·Generate frequent itemsets of length k
		·Repeat until no new frequent itemsets are identified 
			  ·Generate length (k+1) candidate itemsets from length k frequent itemsets
			  ·Prune candidate itemsets containing subsets of length k+1 that are infrequent
			  ·Count the support of each candidate by scanning the DB
·Eliminate candidates that are infrequent, leaving only those that are frequent
```

	D为总集
	1.建立频繁-1-项集
	2.for k=2 频繁-k-项集不为空
					从频繁-k-1-项集中产生候选-K-项集
					计算候选-k-项集的支持度计数
					去除小于支持度计数阈值的集合
					返回频繁-k-项集
	3.返回频繁项集

			产生候选项集：
				从频繁-k-1项集中挑选两项，使得：
					前k-2项相同
				合并这两项

			计算支持度计数：
				for 所有的事务 in D：
					产生这个事务的子集集合
					判断候选项集是否属于这个子集

===========================================================
## 使用FP-Growth算法获取频繁项集

Apriori算法的缺点在于，过多的重复扫描数据库。而另一种方法可以避免这种情况。

 FP-Growth算法是一种基于**FP-Tree(Frequent Pattern Tree)**的算法。
	  算法使用递归解决多个子问题的方式来产生频繁项集，相比apriori也更容易实现。

### FP-Tree Frequent Pattern Tree | 频繁模式树：

		Frequent pattern tree是一种前缀树，它可以看做是所有事务的投影。

		建树方法：insert_fp_tree(p|P,T)

			首先我们统计出所有的频繁一项集。
			然后对所有的事务进行过滤和排序(降序)。
			把每一条事务看作是(头|后缀)的形式，后缀可以为空。
			然后递归的调用insert_fp_tree()
				每次进行判断:
					若当前结点的儿女中有p，那么这个儿女的频繁度+1
					若没有，新建这个儿女，频繁度设置为1
				然后递归地，把后缀P进行分割，然后对下一个结点进行insert_fp_tree()

### Header_Table：

			  Header_Table的存在是为了方便进行快速查找。
			  Header_Table有三个域：item_name,count,next

				item_name是这个item的唯一标识符
				count是这个item的支持度
				next指向FP-Tree中的一个item的点，并且连接成串

### Conditional Pattern Base | 条件模式基：

			  条件模式基是由FP-Tree中所有的item的前缀构成的。
			  从事务数据的角度上讲，就是包含item(可以不止一个元素)的所有事务集

			generate conditional pattern base:
				  构造一个条件模式基，我们利用Header_Table对所有的item进行浏览，然后保存前缀路径即可。

###Conditional Pattern Tree | 条件模式树：

			  从条件模式基构建出的FP-Tree称为条件模式树。

			  对条件模式基中所有item的频繁度进行加和，然后用阈值过滤，新的由过滤后的前缀路径形成的FP-Tree就是条件模式树了。

### 判定：

			  如果一个item(可以不止一个)对应的条件模式树为空或者仅有一条路径。
			  那么：
				  item∪tree即为一个频繁项集。由apriori定理1可知，其中所有的子集都是频繁项集。

-----
 这次就不祝OIer了。。。反正你们也用不到23333

 ![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/dHRf7aqsS5vnznXchbG4vNBIy7F4WFsGG*TB5TefLe8!/r/dPMAAAAAAAAA)

