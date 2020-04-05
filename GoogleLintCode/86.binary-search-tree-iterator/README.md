## Description

Design an iterator over a binary search tree with the following rules:

+ Elements are visited in ascending order (i.e. an in-order traversal)
+ next() and hasNext() queries run in O(1) time in average.

## Solution

二叉查找树的性质非常的明显, 想要得到所有元素的有序序列的方式是使用递归. 递归本质意义上是使用函数调用栈实现的, 因此我们将有序序列输出使用迭代器方式进行实现的时候, 可以使用栈来进行递归的模拟.

很明显, 栈中还有元素时, 即输出还没有结束. 则栈中有元素时, next()将栈顶元素弹出即可.

当弹出一个元素的时候, 需要将该元素后的数加入栈, 该元素的next有两种可能, 一是该元素的父亲(即该元素为其父亲的唯一左儿子), 二则是该元素右子树的最左子孙(也就是比该元素大的数字中最小的). 在查找该元素的时候将其路径全部压入栈即可.
