---
title: Trapping Water
date: 2017-11-03 18:06:10
tags:
- Array
- Stack

---



Given *n* non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

For example, 
Given `[0,1,0,2,1,0,1,3,2,1,2,1]`, return `6`.

![img](http://www.leetcode.com/static/images/problemset/rainwatertrap.png)

The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.

这道题也是 **HARD** 难度，需要仔细分析一下。

首先先考虑，为何water会被trap？因为左边和右边都比中间的元素大，也就是说，只有出现valley的一组数据才会trap water。那么要如果计算这些valley呢？即上升、下降、再上升。当出现再上升的时候，就是满足trap water的时候。当然我们可以找出所有满足**上升**、**下降**、**再上升**的集合再求总和，但是代码其实很难写，因为这些集合是连续的，前面的**再上升**就是下一个第一次**上升**。

但是，至少这给了我们一个思路，就是我们可以通过遍历的手段是找出上升、下降、再上升的组合。怎么做呢？我们可以使用 Stack 来 track，什么意思呢？按照FILO 的 stack，顶部元素一定是最近遍历过的元素，那么只要比他大，那就是上升，比他小，就是下降。

可是我们需要的是**上下上**的组合呀。不，其实我们并不care第一次上，因为trap water的时候，只有最高的那个会被利用上，而之前的都会被丢弃。第一次**上**其实具体点说，是一个递增序列的最后最高的元素，我们只需要保留哪个最后最高就可以了，前面矮的都可以丢弃。因此，如果遇到递增序列，就直接丢弃顶部元素，push新的higher 元素。

那么怎么处理下呢？首先，遇到下就push，因为你需要这个当前小的值来作为计算amount的底（长方形面积计算公式）。其次，其实仅仅下还不够，必须还有后面跟随的一个上，才能形成valley。因此遇到下，不用停，继续遍历直到遇到第一个上，形成valley，就可以trap water。而此时能trap water amount就是 `amount = bottom * height` where `bottom = i - stack.peak() - 1` and  `height = min(h[i], h[stack.peak()]) - h[stack.pop()]`

代码如下:

```python
class Stack:
    
    def __init__(self):
        self.items = []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        return self.items.pop()
    
    def peek(self):
        return self.items[len(self.items) - 1]
    
    def isEmpty(self):
        return len(self.items) == 0

class Solution(object):
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        if height is None or len(height) < 3:
            return 0
        stack = Stack()
        i = 0
        water = 0
        while i < len(height):
            while not stack.isEmpty() and height[i] > height[stack.peek()]:
                bot = height[stack.pop()]
                if not stack.isEmpty():
                    left = height[stack.peek()]
                    right = height[i]
                    water += ((min(left, right) - bot) * (i - stack.peek() - 1))
            stack.push(i)
            i += 1
        return water
        
```



