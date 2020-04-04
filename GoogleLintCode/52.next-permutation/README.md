## Description

Given a list of integers, which denote a permutation.

Find the next permutation in ascending order.

## Solution

从一个小规律开始解题, 从后往前递增的序列是最大的排列.

因此, 从后往前找到不是递增的第一个数字, 如果找不到, 则直接将整个排列翻转. 否则设这个数的下标为index.

1~index之间的数字, 都不需要改变, 只需要将index位置的数字稍微变大一点即可, 因此从index~n之间找到仅比index位置上的数字大的那个, 然后交换即可.
