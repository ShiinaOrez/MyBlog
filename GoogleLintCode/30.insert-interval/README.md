## Description

Given a non-overlapping interval list which is sorted by start point.

Insert a new interval into it, make sure the list is still in order and `non-overlapping` (merge intervals if necessary).

## Solution

根据所给的区间, new_interval(left, right), 二分寻找到对应的两头区间. 然后进行合并插入即可.

分为几种情况:

+ 1. 新区间在最左
+ 2. 新区间在最右
+ 3. 其他情况, 二分查找到两头的区间, 然后进行合并插入.
