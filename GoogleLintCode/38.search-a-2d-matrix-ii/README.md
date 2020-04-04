## Description

Write an efficient algorithm that searches for a value in an m x n matrix, return the occurrence of it.

This matrix has the following properties:

+ Integers in each row are sorted from left to right.
+ Integers in each column are sorted from up to bottom.
+ No duplicate integers in each row or column.

## Solution

解决这个问题也很简单, 和地理上的等高线是一个道理. 同一数值在矩阵上有一条从左下到右上的轨迹. 因此直接从左下找到右上, 就可以O(n+m)解决这个问题.
