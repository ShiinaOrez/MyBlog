# 数据库中索引二三事

这篇文章是最近学习数据库索引的总结, 杂七杂八的东西比较多. 可能引起观感较差.

## 目录

+ [索引](#索引)
  + 产生索引的原因
  + 索引的底层数据结构
  + 索引的种类
  + 如何定义一个索引
+ [最左前缀匹配原则](#最左前缀匹配原则)
  + 什么是最左前缀匹配原则
  + 一些例子
+ [保证索引生效](#保证索引生效)
  + 如何改写不生效的语句
  + 优化like语句
+ [比较有无索引的性能差距](比较有无索引的性能差距)
  + 实验设计
  + 实验代码
  + 运行结果

## 索引

关系型数据库中的索引(index), 是为了加速数据库查询的一种手段.

### 产生索引的原因

在一个关系型数据库中, 我们往往有这样的需求: 查询某条记录, 对这些记录以某一列值为关键字进行排序等等... 但是对于大量的数据而言, 每次都将所需要的数据进行排序是不是太浪费计算机系统资源呢? 因此人们在数据库上建立了一种用于辅助查询和排序的数据结构: **索引**.

### 索引的底层数据结构

索引的底层数据结构必须是能够存储记录间按某个关键字进行大小比较结果的数据结构, 我们能够第一时间想到的是: 二叉排序树. 但是面对海量的数据, 二叉排序树的性能其实也没有很好, 因为是二叉的, 所以树的高度会相对而言比较高一些. 所以一般的数据库引擎索引所使用的数据结构是K路平衡排序树, 也就是我们经常听到的**B-Tree**.

#### B-Tree

B-Tree的主要性质如下:
+ 对于B-Tree定义一个常量K, 每个B-Tree的节点的儿子节点不能多于K/2个
+ 每个节点可以记录多个关键值, 节点的儿子节点分别对应这些关键值的区间
+ 当一个节点插入导致儿子节点数过多的时候, 触发节点的分裂, 节点的分裂是B-Tree长高的唯一途径

![B-Tree Image From Wiki](https://upload.wikimedia.org/wikipedia/commons/6/65/B-tree.svg)

对于B-Tree的详细操作和实现, 可以在[WikiPedia: B-Tree](https://en.wikipedia.org/wiki/B-tree)中进行学习, 国内的网站一般都讲的很乱. 也很少会有详细讲解B-Tree的, 一般都是讲解B+Tree, 因为很功利的目的: 国内面试只会问你B+Tree, 而不会问你B-Tree.

B+Tree是什么呢? 其实就是在B-Tree的基础之上, 在相邻的兄弟节点之间加上了顺序访问的指针, 能够加速对于整个有序数据列表的访问. 在现在的数据库中, 大多使用B+Tree作为底层的索引数据结构.

![B+Tree Image From Wiki](https://upload.wikimedia.org/wikipedia/commons/3/37/Bplustree.png)

### 索引的种类

在这里, 以MySQL为例进行说明.

+ 1. 普通索引
  + 最最最最平常的索引, 使用我们提到的B+Tree为底层数据结构, 也是我们最常用的索引.
+ 2. 唯一索引
  + 和普通索引类似, 但是必须保持创建索引列数据值的唯一性, 如果这个唯一索引组合了多个列, 那么多个列的值的组合必须是唯一的.
+ 3. 全文索引
  + 全文索引是MySQL中逐渐支持的一种索引, 必须建立在CHAR, VARCHAR和TEXT之上, 也就是类字符(串)类型上, 会对于列的内容进行分词再进行检索.

### 如何定义一个索引

创建一个带有普通索引的表:

```mysql
CREATE TABLE `tb_test` (
  `id`     UNSIGNED INT(11) NOT NULL AUTO_INCREMENT,
  `f_name` VARCHAR(20) NOT NULL,
  `l_name` VARCHAR(20) NOT NULL,
  
  PRIMARY KEY (`id`),
  INDEX `idx_name` (`name`),
  INDEX `idx_f_l_name` (`f_name`, `l_name`)
);
```

这样, 在`tb_test`这个表中, 我们建立了两个普通索引: 其中一个只对应一列, 而另一个组合了两列.

------

创建一个带有唯一索引的表:
```mysql
CREATE TABLE `tb_edge` (
  `id`   UNSIGNED INT(11) NOT NULL AUTO_INCREMENT,
  `from` UNSIGNED INT(11) NOT NULL,
  `to`   UNSIGNED INT(11) NOT NULL,
  
  PRIMARY KEY (`id`),
  UNIQUE INDEX `idx_from_to` (`from`, `to`)
);
```

在这个表中, 我试图去存储一个图中的边, 不管是有向图还是无向图, 我们都希望对于一条边, 仅仅存储一次, 这个时候就可以创建一个唯一组合索引.

------

创建全文索引: (注意你的MySQL版本和你使用的引擎! MyISAM在MySQL v3.23.23后是可用的, 而InnoDB在MySQL v5.6之后才支持全文检索!)
```mysql
CREATE TABLE `tb_article` (
  `id`      UNSIGNED INT(11) NOT NULL AUTO_INCREMENT,
  `title`   VARCHAR(30)      NOT NULL,
  `author`  VARCHAR(20)      NOT NULL,
  `content` TEXT,
  
  PRIMARY KEY (`id`),
  FULLTEXT INDEX `idx_fulltext` (`title`, `author`, `content`)
);
```

全文检索的应用场景经常出现在一个实体的多个列均需要分词搜索的场景, 最典型的就是类似于文章, 博客, 分享这种, 用户希望输入一个关键字来进行查询, 而关键字可能是对于文章标题, 文章作者或者文章内容进行筛选.

需要注意的是, 建立了全文索引之后, 使用的方式和其他的索引有很大不同, 需要使用`AGAINST`和`MATCH`关键字:

```mysql
SELECT id FROM `tb_article`
WHERE
MATCH(`title`, `author`, `content`)
AGAINST('keyword');
```

其中的`MATCH`关键字用于告知对于哪些列进行全文检索, 而`AGAINST`中则放置对应的值.

## 最左前缀匹配原则

最左前缀匹配原则是MySQL中决定是否使用索引, 使用哪一条索引的推导规则, 通过对于查询语句从左到右的推导过程决定对于索引的使用.

### 什么是最左前缀匹配原则

在MySQL中, 匹配索引的原则是**最左优先**, 也就是我们提到的最左前缀匹配原则.

在我们创建联合索引的时候, 本质上创建的索引的个数是索引所联合的列数n, 举个例子, 我们使用
```mysql
INDEX `idx` (`one`, `two`, `three`);
```
创建了一个联合索引, 但是实际上我们创建了三个索引:
```mysql
(`one`), (`one`, `two`), (`three`)
```
也就是说, 我们在进行查询的时候, 不必将`one`, `two`, `three`的全部值都给出才能利用索引, 而是只需要满足最左前缀匹配原则产生的索引就可以了.

比如说:
```mysql
SELECT * FROM TABLENAME WHERE `one` = val_1;
SELECT * FROM TABLENAME WHERE `one` = val_1 AND `two` = val_2;
```
也都将能够命中我们创建的索引.

将**顺序颠倒**也是**可以的**, 因为MySQL会自动帮我们调整为最优的顺序
```mysql
SELECT * FROM TABLENAME WHERE `two` = val_2 AND `three` = val_3 AND `one` = val_1;
```
上面的语句也是可以成功命中索引的.

**模糊匹配字符串时, 如果没有在开头使用通配符`%`, 则可以命中索引**:

```mysql
SELECT * FROM TABLENAME WHERE `name` LIKE 'key%word';
SELECT * FROM TABLENAME WHERE `name` LIKE 'keyword%';
```
以上两种情况都是可以成功命中索引的, 但是通配符的位置会影响对于索引使用的效率, 越靠左效率越低, 直至`%`出现在最左边, 则根本不适用索引.

**当我们使用范围查询的时候, 如果范围查询的那一列符合最左前缀匹配原则, 则可以使用到索引, 但是范围查询之后的列全部不能使用索引.**
```mysql
SELECT * FROM TABLENAME WHERE `one` = val_1 AND `two` > val_2 AND `three` = val_3;
```
则在上面的语句中, 会使用到索引 
```mysql
(`one`, `two`)
```

**最后一条, 如果查询语句中, 含有函数或者表达式, 则直接放弃使用索引.**

## 保证索引生效

## 比较有无索引的性能差距
