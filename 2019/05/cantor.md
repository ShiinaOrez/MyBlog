---
title: 康托展开 Cantor Expansion
date: 2019-05-06 19:52:43
tags: Algorithm
---

## 全排列一直是一个让人联想到爆炸复杂度的东西
如果一件事情牵扯到了全排列，大多数人脑中一定都是这样子的：高中学习的排列组合、排列数、组合数、数不清的阶乘、大学的组合数学等等一系列让人头疼的东西。

如果你写过一点点OI相关的题目，或者在Leetcode等网站上刷过题，那么你一定使用过各种语言写过这样的题目：生成全排列。考点大概就是回溯。

n的数有n！个全排列，生成的算法也有很多种：字典序法，插入法，邻为对换法，递增进位制法等等。不论你们去简化过程，每个全排列总还是要遍历到的。

不过既然全排列一定是有序的，我们一定可以通过某一个数来确定一个唯一的全排列吧！

是的，这个想法是对的，这就是康托展开（Cantor expansion）。

## 康托展开的基本思想
一个全排列一定是由1～n的不重复的数字组成的。而生成的全排列一定是有序的：我们假设就是字典序吧。那么每一位其实就可以确定在这之前有多少全排列。

以 3 5 7 4 1 2 9 6 8 为例：

```
3 5 7 4 1 2 9 6 8
↑
3之前有 3-1=2 个以1或者2开头的全排列，共有(9-1)! * (3-1) 个
3 5 7 4 1 2 9 6 8
  ↑
5之前除去3有5-1-1=3 个以1或2或4开头的长度为7的全排列，共有(9-2)! * (5-2)个
...
```

按照这样的情况我们就可以使用一个式子来表示3 5 7 4 1 2 9 6 8之前有多少个全排列了：

```
2 * 8! + 3 * 7! + 4 * 6! + 2 * 5! + 0 * 4! + 0 * 3! + 2 * 2! + 0 * 1! + 0 * 0!
```
## 代码实现？
最近在写Go嘛，所以就...
```go
func reverseCantor(n int, k int) string {
    /*
     * 逆康托展开，接收一个数字返回相应的全排列
    */
    //存放阶乘
	facs := []int{1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880}
    nums := []int{}
    for i:=1; i<=n; i++ {
        nums = append(nums, i)
    }
    res := []int{}
    k -= 1
    for j:=n; j>=1; j-- {
        r := k % facs[j-1];
        t := k / facs[j-1];
        k = r;
        res = append(res, nums[t])
        nums = append(nums[:t], nums[t+1:]...)
    }
    bytes := []byte{}
    for _, v := range res {
        bytes = append(bytes, byte(48 + v))
    }
    return string(bytes)
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506183209389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6,size_16,color_FFFFFF,t_70)
