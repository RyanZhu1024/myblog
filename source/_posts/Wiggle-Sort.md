---
title: Wiggle Sort
date: 2017-11-03 18:07:42
tags:
- Array
- Sorting
- 脑筋急转弯

---



> Given an unsorted array `nums`, reorder it **in-place** such that `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.
>
> For example, given `nums = [3, 5, 2, 1, 6, 4]`, one possible answer is `[1, 6, 2, 5, 3, 4]`.

这道题呢看上去好像一开始摸不着头脑，我一开始也是傻傻的一个一个sort过去，但其实我们来仔细观察一下，sort之后的array是个怎样的结果呢？

你会发现，我们假设下面从1开始，奇数项都是小于两边偶数项的，也就是说，碰到奇数项，你就做3个数的比较，`i`, `i - 1`, `i + 1`,让中间的数最小，两边数都比它大就好了。

同样的，对于偶数，那就只要保证中间的大，两边的小就行了。

基于以上发现，我们代码就好写很多了：

```python
class Solution(object):
    def wiggleSort(self, nums):
        """
        :type nums: List[int]
        :rtype: void Do not return anything, modify nums in-place instead.
        """
        if nums == None:
            return
        self.sortOdd(nums)
        self.sortEven(nums)
    
    def sortOdd(self, nums):
        i = 1
        while i < len(nums):
            if nums[i] < nums[i - 1]:
                nums[i], nums[i - 1] = nums[i - 1], nums[i]
            if i < len(nums) - 1 and nums[i] < nums[i + 1]:
                nums[i], nums[i + 1] = nums[i + 1], nums[i]
            i += 2
    
    def sortEven(self, nums):
        i = 0
        while i < len(nums):
            if i > 0 and nums[i] > nums[i - 1]:
                nums[i], nums[i - 1] = nums[i - 1], nums[i]
            if i < len(nums) - 1 and nums[i] > nums[i + 1]:
                nums[i], nums[i + 1] = nums[i + 1], nums[i]
            i += 2
                
```

