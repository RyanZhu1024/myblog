---
title: Subarray Sum Equals K
date: 2017-11-03 18:03:11
tags:
- Array
- preSum
- Map

---

# [Go to problem](https://leetcode.com/problems/subarray-sum-equals-k/)

Given an array of integers and an integer **k**, you need to find the total number of **continuous** subarrays whose sum equals to **k**.

**Example 1:**

```
Input:nums = [1,1,1], k = 2
Output: 2
```

这又是一道和 Subarray 有关的题目。在解这道题之前，我们先来看看一道更简单的。

> 在一个 array 里面寻找一个二元组，使其和等于 K 

它有几种解法，不过其中一个最简单也最快，就是用一个 Map 存储所有见过的 value 和他们对应的 Index，通过 `Map.containsKey(K - value)` 来得出最终结果。

> 在一个 array 里面寻找二元组的个数，使其和等于 K 

换个问法，结果就不一样，这次求的是number of pairs instead of one solution. 然而解法依然不变，仅仅在之前的解法上加入一个counter就解决了问题

OK，那接下来我们来看原题。

原题和我们给出的简单题区别仅仅只是把value换成了 sum of subarray. 那么这对算法有影响不？NO！

我们依然可以用一个Map去存储 `currentSum`，然后用 `currentSum - K`，也就是判定条件换做 `Map.containsKey(currentSum - K)` where currentSum is an accumulated sum as you iterate over the array. 这个做法的assumption即是题目里说的，subarray必须是连续的。

举个例子：

> 1,2,3,4,5, k = 7
>
> 7 = 3 + 4 (continuous) != 2 + 5 (not continuous)
>
> 7 = 1 + 2 + 3 + 4 - (1 + 2)

以下是代码:

```python
class Solution:
    def subarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        preSumMap = {}
        preSumMap[0] = 1
        tempSum = 0
        count = 0
        for i in range(len(nums)):
            tempSum += nums[i]
            count += preSumMap[tempSum - k] if tempSum - k in preSumMap else 0
            preSumMap[tempSum] = preSumMap[tempSum] + 1 if tempSum in preSumMap else 1
        return count
```

