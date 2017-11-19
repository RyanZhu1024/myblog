---
title: Create Max Number
date: 2017-11-18 23:17:57
tags:
- Array
- Dynamic Programming

---

[Go Here](https://leetcode.com/problems/create-maximum-number/description/)

给出两个数组，从中选出K个，造出一个最大的数字；可以认为K一定小于两个数组长度之和。造出的数字的每个digit必须保持原本的相对顺序。

分析：

1. 最大数字的每个digit都来自两个数组。
2. 一共K个digits，每个都必须保留原本的相对顺序
3. 那么我们可以认为，m个来自数组1， k - m个来自数组2
4. 我们可以遍历所有可能的m，分辨从数组1里找出长度为m的最大数和数组2里长度为k-m的最大数
5. 然后组成一个最大数
6. 最后计算全局最大数

代码：

```python
class Solution:
    def maxNumber(self, nums1, nums2, k):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :type k: int
        :rtype: List[int]
        """
        len1, len2 = len(nums1), len(nums2)
        res = []
        for i in range(k + 1):
            # 尝试每个i，从0，到k
            if i <= len1 and k - i <= len2:
                fromNum1 = self.getMax(nums1, i)#nums1里取i个
                fromNum2 = self.getMax(nums2, k - i)#nums2里取k-i个
                temp = self.merge(fromNum1, fromNum2)#造出最大的数字
                res = max(res, temp)#计算全局最大
        return res
    
    #python的特殊用法，最大数字就是比较两个数组的合成值，谁大，就pop谁
    def merge(self, a, b):
        return [max(a, b).pop(0) for _ in a+b]
  
	#选取长度为k的最大值，为了保留相对顺序，那么我们可以认为，第一个最大值在0到n - k + 1里选择，第二个最大值在前面那个最大值的下标后一个开始到，n - k + 1里选择。每次存储选择的下标，最后返回组成的数字
    #例如： 2，10，2，3，4， k = 3
    # 一共选取三次，分别在
    #[2,10,2]，下标0开始，选取10,
    #[2, 3], 下标2开始，选取3
    #[4]，下标4开始，选取4
    def getMax(self, nums, k):
        n = len(nums)
        ans = []
        while k > 0:
            start = 0 if len(ans) == 0 else (ans[-1] + 1)
            end = n - k + 1
            ans.append(nums[start:end].index(max(nums[start: end])) + start)
            k -= 1
        return [nums[i] for i in ans]
```

