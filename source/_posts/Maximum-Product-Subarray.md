---
title: Maximum Product Subarray
date: 2017-11-08 21:42:02
tags:
- Array
- Dynamic Programming

---

> Find the contiguous subarray within an array (containing at least one number) which has the largest product.
>
> For example, given the array `[2,3,-2,4]`,
> the contiguous subarray `[2,3]` has the largest product = `6`.

[Go here](https://leetcode.com/problems/maximum-product-subarray/description/)

这道题和[Maximum Subarray](https://ryanzhu1024.github.io/2017/11/04/Maximum-Subarray/)其实是很类似的。因此，思路也一样。仅仅是针对乘法做了修改。在 Maximum Subarray里面，我们做加法，因此只需取和的最大值，但是在乘法里面，积最大即可以来自两个最大的自然数，也可以是来自两个最小的负数，因此我们需要两组变量，`maxHere, minHere, maxSoFar`。代码如下：

```python
class Solution:
    def maxProduct(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if nums is None or len(nums) == 0:
            return 0
        minHere, maxHere, maxSoFar = 1, 1, -10000000
        for n in nums:
            tempMin = min(n, min(minHere * n, maxHere * n))
            tempMax = max(n, max(minHere * n, maxHere * n))
            minHere = tempMin
            maxHere = tempMax
            maxSoFar = max(maxHere, maxSoFar)
        return maxSoFar
```

