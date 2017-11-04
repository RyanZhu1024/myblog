---
title: Median of 2 sorted arrays
date: 2017-11-03 18:01:38
tags:
- Array
- Divide and Conquer

---

There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

这道题呢特别2，因为他更general的问题其实是median of K sorted arrays，那种情况用一个priority queue就完了，但是这里确实 k = 2，虽然priority queue也可用，但是需要消耗空间，面试中碰到估计肯定要被问怎样在constant space里完成，然后就没有然后了。

先来说说他的规律，其实很简单：两个sorted arrays的中位数，你可以分别给出两个arrays各自的中位数，如果**左边的小于右边**的，那merge之后的中位数一定在**左边中位数的右边**，相反，如果**左边的大于右边**的，那merge后的中位数一定在**右边中位数的左边**。

这听上去就很绕，那么接下来直接用一段伪代码来解释：

```python
arr1, arr2 [1,5,10], [2,6,8]
mid1, mid2 {5}, {6}
mid1 <= mid2 
realMid >= mid1, realMid will be on the right of mid1 when merge 5.5 > 5
mid1 > mid2  [1,5,10], [2,3,8] {5} {3}
realMid >= mid2, realMid will be on the right of mid2 when merge  4 > 3
```

除了上述规律，还要注意各种corner case，就是越界。为何会越界，就是对于其中一个数组的begin，end下标不再小于长度了，或者begin > end了。为何简化coding，我们自然不会直接去计算 （begin + end）/  2，而是选用 `K = size >> 1`，然后通过offset k 来计算`mid = begin + k >> 1`

以下为code，建议理解后背出就好了。

```python
class Solution:
    def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        return self.findMedian(nums1, nums2)
    
    def findMedian(self,arr1, arr2):
        length = len(arr1) + len(arr2)
        return (self.findKth(arr1, 0, arr2, 0, length + 1 >> 1) + self.findKth(arr1, 0, arr2, 0, length + 2 >> 1)) / 2

    def findKth(self,arr1, s1, arr2, s2, k):
        while s1 < len(arr1) and s2 < len(arr2) and k > 1:
            mid1 = sys.maxsize if (s1 + (k >> 1) - 1 >= len(arr1)) else arr1[s1 + (k >> 1) - 1]
            mid2 = sys.maxsize if (s2 + (k >> 1) - 1 >= len(arr2)) else arr2[s2 + (k >> 1) - 1]
            s1, s2 = (s1 + (k >> 1), s2) if mid1 <= mid2 else (s1, s2 + (k >> 1))
            k = k - (k >> 1)
        if s1 >= len(arr1):
            return arr2[s2 + k - 1]
        if s2 >= len(arr2):
            return arr1[s1 + k - 1]
        return min(arr1[s1], arr2[s2])
        
```

