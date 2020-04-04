## Description

Design an algorithm and write code to serialize and deserialize a binary tree. Writing the tree to a file is called 'serialization' and reading back from the file to reconstruct the exact same binary tree is 'deserialization'.

## Solution

简单的递归, 假设元素之间以","进行分割, 空叶子节点使用"#"进行标识, 则进行简单递归便可以序列化/反序列化一棵二叉树. 可以采用先序遍历, 中序遍历和后序遍历三种方式.
