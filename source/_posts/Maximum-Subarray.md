---
title: Maximum Subarray
date: 2017-11-04 15:53:40
tags:
- Array
- Dynamic Programming

---

> Find the contiguous subarray within an array (containing at least one number) which has the largest sum.
>
> For example, given the array `[-2,1,-3,4,-1,2,1,-5,4]`,
> the contiguous subarray `[4,-1,2,1]` has the largest sum = `6`.

[Go to here](https://leetcode.com/problems/maximum-subarray/description/)

这道题乍看之下可以用brute force解决，但是那样太费时，需要计算所有的combinations，阶乘级别的。

那么如何加速呢？既然是个subarray相关的题目，之前的题目里出现过类似dynamic programming的思路。那么我们可以试试看DP。

在DP里我们知道，要求当前的 `i` 值，就得先求的之前 `i - 1` 的值。那么既然要求整个array里面的最大sum of subarray，那就先得出最大subarray 里面的 sum of subarray。

举个例子：

1. 数组：-2； 结果 0。只有一个元素
2. 数组：-2， 1； 结果 1。一个元素 -2 ， -2 + 1，和 一个元素 1 比较
3. 数组：-2，1，-3； 结果 1。一个元素 -2 ， -2 + 1，一个元素1，1 - 3， 一个元素 -3 比较
4. 数组：-2，1，-3，4：结果4。一个元素 -2 ， -2 + 1，一个元素1，1 - 3， 一个元素 -3， 1 - 3 + 4 和一个元素 4 比较
5. 。。。。。。。

根据上面的例子，我们发现，如果在之前的和上面加上当前值小于直接使用当前值的话，我们便会舍弃之前的和，然后从当前的值，重新遍历。

这个算法总结下来就是用两个变量来track local max 和 global max。（是不是很像之前的买卖股票？）`localmax = max(localmax + current, current)`, `globalmax = max(globalmax, localmax)`

代码：

```python
class Solution:
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if nums == None or len(nums) == 0:
            return 0
        maxUntilHere = 0
        maxSoFar = -2147483647
        for n in nums:
            maxUntilHere = max(maxUntilHere + n, n)
            maxSoFar = max(maxSoFar, maxUntilHere)
        return maxSoFar
```



