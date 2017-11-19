---
title: Russian Doll
date: 2017-11-18 23:52:58
tags:
- Array
- Dynamic Programming
- Binary Search

---

[Go Here](https://leetcode.com/problems/russian-doll-envelopes/description/)

这道题和longest increasing sub-sequence 几乎一样，仅仅需要一开始做一次排序，以及一个特殊顺序处理，comments里会提到。

上代码

```python
import bisect
class Solution:
    def maxEnvelopes(self, envelopes):
        """
        :type envelopes: List[List[int]]
        :rtype: int
        """
        if not envelopes:
            return 0
        l = len(envelopes)
        if l == 1:
            return 1
        #先把信封按照宽度排序，既然要一个套一个，我们可以递增排序，但是如果宽度一样，那么我们希望此时按照长度递减排序，因为(2,3),(3,3),(3,4)会出现前两个套不起来的情况，但是如果是(2,3), (3,4), (3,3)就没有问题了。python的排序真是方便
        envelopes.sort(key=lambda c:(c[0], -c[1]))
        # 此时再通过对长度做longest increasing sub-sequence就能求出解
        return self.lis(list(map(lambda c: c[1], envelopes)))
    
    def lis(self, arr):
        q = []
        for i in arr:
            pos = bisect.bisect_left(q, i)
            if len(q) == pos:
                q.append(i)
            else:
                q[pos] = i
        return len(q)
```

