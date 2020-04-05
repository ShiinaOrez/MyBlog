## Description

Numbers keep coming, return the median of numbers at every time a new number added.

What's the definition of Median?

+ The median is not equal to median in math.
+ Median is the number that in the middle of a sorted array. If there are n numbers in a sorted array A, the median is A[(n - 1) / 2]A[(n−1)/2].
+ For example, if A=[1,2,3], median is 2. If A=[1,19], median is 1.

## Solution

建立两个堆, 一个大根堆一个小根堆, 大根堆存储整体数据流中左半部分的数值, 小根堆存储右半部分的数值. 则大根堆的根即为答案.

每一个输入数据流的数据, 都需要轮流先插入大根堆, 然后再将大根堆的根插入小根堆. 这样就保证了两个堆分别存储左右两部分. 即大根堆所有值都小于小根堆所有值.

