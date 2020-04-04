## Description

There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays.

## Solution

二分答案. 不断的对答案进行二分逼近.

已知两个已经排序好的数组A和B, 我们可以知道的只是所有数中的最小值和最大值. 即min = min(A[0], B[0]), max = max(A[lengthA-1], B[lengthB-1]). 因此我们假设答案mid就是(min+max)/2, 得到mid之后, 验证答案和真实答案的偏离值.

我们通过二分查找的方式可以找出A数组和B数组中分别大于和小于mid的值的个数, 就能知晓mid是大于真正的答案还是小于真正的答案. 若大于, 则调整mid为max, 继续二分. 若小于, 则调整mid为min, 进行二分. 最终逼近答案.
