---
title: Longest Increasing Sub-sequence
date: 2017-11-18 23:48:43
tags:
- Array
- Dynamic Programming
- Binary Search

---

[Go Here](https://leetcode.com/submissions/detail/128849519/)

直接上代码，关键还是python的写法太简洁了。

```python
import bisect
class Solution:
    def lengthOfLIS(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        dp = []
        for n in nums:
            pos = bisect.bisect_left(dp, n)
            #二分搜索每个元素在dp数组里的位置，由于递增序列，dp数组可以用二分搜索直接找到当前元素应该去的位置。假如在中间，那么可以直接替换并且并不影响数组的长度，假如是末尾，直接追加，即当pos=len(dp)
            if pos == len(dp):
                dp.append(n)
            else:
                dp[pos] = n
        return len(dp)
```

