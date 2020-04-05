## Description

There is an integer array which has the following features:

+ The numbers in adjacent positions are different.
+ A[0] < A[1] && A[A.length - 2] > A[A.length - 1].

We define a position P is a peak if:

	A[P] > A[P-1] && A[P] > A[P+1]

Find a peak element in this array. Return the index of the peak.

## Solution

这个题就很简单了, 寻找峰值, 所谓峰值就是导数为0的点. 题目保证了左端的导数>0, 右端的导数<0, 则从两端二分逼近一定能找到导数为0的峰值点.
