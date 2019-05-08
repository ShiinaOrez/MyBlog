---
title: 自平衡二叉查找树 -- AVL树 Adelson-Velsky-Landis Tree
date: 2019-05-08 18:24:59
tags: Algorithm DataStructure
---

## 平衡还是不平衡，这是一个问题
😳：平衡好不好呢？
🤣：我说它好。
😳：好在哪里呢？
🤣：我只能告诉你无可奉告。

以上的对话是大多数人的反应，对于数据结构中的树形结构为什么平衡会比较好不求甚解。只是大概会比不平衡好吧，毕竟万物都是追求和谐的。

树形结构的平衡好不好？当然好了，这就是产生树的基本思想啊，因为极端的不平衡就是线性表啊。

所谓树形结构的平衡性，就是指尽量使树中的节点的子树的深度都大致相同，最理想的情况就是类似于完全二叉树那样的形状。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190508150412872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6,size_16,color_FFFFFF,t_70)
平衡带来的好处有什么呢？
🤣操作时间复杂度低，诸如查询，插入，删除等等curd
🤣好像没了，但是数据结构不就是最在意这个吗

-----

## 什么是二叉查找树？
这一节大概讲的都是废话。。一般来看这个文章的应该都知道二叉查找树是什么吧。。

Binary Search Tree，BST就是二叉查找树，遵循最基本的“查找树”的性质：

+ 对于树中的任意一个节点：
	+ 如果这个节点的左子树不为空，则左子树上的所有节点都小于该节点的值
	+ 如果这个节点的右子树不为空，则右子树上的所有节点都小于该节点的值

也就是：[左子树的所有的节点] 该节点 [右子树的所有的节点]
是不是长的和**快速排序**有点像？

就是因为维持了这样的性质，达到了很好的效果：
+ 所有的数据是有序的
+ 查找数据的时间和二分查找几乎等同（平衡时相等）

### 为什么说平衡时相等？
考虑最极端的情况嘛。。就是一个链条，这样的时间复杂度就是O(N)了

-----
## 如何达到自平衡的效果？
AVL树中引入了平衡因子的概念：

	平衡因子：某个结点的左子树的高度减去右子树的高度得到的差值。

也就是说，一个平衡的树就是所有的节点的平衡因子的绝对值都是小于等于1的。

🌛 🌜：爱的魔力转圈圈～

**旋转**，通过旋转来达到自平衡。

问题来了，现在相信大家对于旋转是个什么鬼东西很疑惑

### 什么是旋转？
我们假设我们有一棵平衡的树，然后我们按照二叉搜索树的原则进行插入：我们大概率会破坏这棵树的平衡性。
最常见的一种情况便是：

```
                🤣                                    🤣
               /                ---- >               /  
             😜                                    😜
                                                  /
                                                🙄
```
这简直太糟糕了，这样下去我们的可爱的BST就要变成链表了！
我们不能放纵我们的可爱的BST，不能让它随意生长，因此我们要：旋转。

在这里我们可以看出 🤣 > 😜 > 🙄，而这种情况下😜作为根节点（父节点）才是最优的选择。
因此我们就旋转一下：
```
                🤣                                    😜
               /                ---- >               /  \
             😜                                    🙄    🤣
            /
          🙄
```

这种情况称之为左左情况：在本来树向左倾斜的情况下又在左子树增加元素。进行右旋。
相同的：右右情况进行左旋。
左右情况：先左旋后右旋。
右左情况：先右旋后左旋。

## 如何实现？
### 节点 - Node
一个节点应该拥有哪些属性呢？

🤷‍♂️肯定要有左右子节点的指针吧！
🤷‍♂️要有平衡因子，嗯，这样才知道什么时候应该旋转。
🤷‍♂️为了方便计算平衡因子，应该缓存一下高度。
```go
type Node struct {
    Data    int
    Height  int
    Left    *Node;
    Right   *Node;
}
```

### 递归计算节点的高度

```go
func (root *Node)TreeHeight() int {
    if root == nil {
        return 0
    }
    return max(root.Left.TreeHeight(), root.Roght.TreeHeight()) + 1
}
```

### 计算平衡因子

```go
func (root *Node)TreeGetBalanceFactor() int {
    if root == nil {
        return 0
    }
    return root.Left.Height - root.Right.Height
}
```

### 右旋

```go
func (root *Node)TreeRotateRight() *Node {
    left := root.Left
    
    root.Left = Left.Right // 将将要被抛弃的节点连接为旋转后的 root 的左孩子
    left.Right = root      // 调换父子关系

    left.Height = max(left.Left.TreeHeight(), left.Right.TreeHeight())+1
    root.Right.Height = max(root.Right.Left.TreeHeight(), root.Right.Right.TreeHeight())+1
    return left
}
```

### 左旋

```go
func (root *Node)TreeRotateLeft() *Node {
    right := root.Right

    root.Right = right.Left
    right.Left = root

    root.Left.Height = max(root.Left.Left.TreeHeight(), root.Left.Right.TreeHeight())+1
    right.Height = max(right.Left.TreeHeight(), right.Left.TreeHeight())+1
    return right
}
```

搞懂了吗？ 🤷🏻‍♂️🤷🏻‍♀️ 🤷🏻‍♂️🤷🏻‍♀️ 🤷🏻‍♂️🤷🏻‍♀️ 🤷🏻‍♂️🤷🏻‍♀️ 🤷🏻‍♂️🤷🏻‍♀️ 🤷🏻‍♂️
--------
