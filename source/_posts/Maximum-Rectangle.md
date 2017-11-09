---
title: Maximum Rectangle
date: 2017-11-08 21:48:07
tags:
- Matrix
- Stack

---

> Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.
>
> For example, given the following matrix:
>
> ```
> 1 0 1 0 0
> 1 0 1 1 1
> 1 1 1 1 1
> 1 0 0 1 0
>
> ```
>
> Return 6.

[Go Here](https://leetcode.com/problems/maximal-rectangle/description/)

这道题其实和[此题](https://ryanzhu1024.github.io/2017/11/03/Trapping-Water/)很像，区别只是在何时做出栈。

首先我们来分析下，如果要在一个matrix里面找出一个最大的长方形，就是要底乘高最大。那么如何把matrix变成一个一个长方形来搜索呢？我们可以发现，在matrix里面，我们是把水平方向连续的1和垂直方向连续的1来作为底和高的，那么可以是否可以把连续的1看做一个整体呢？比如说，把所有垂直方向的连续的1看做一个整体，投射到一行里面变成一个一维数组，是不是就变成了water trap类似的题目了呢？

例如例子里的这个matrix，可以变成

```shell
1 0 1 0 0
2 0 2 1 1
3 1 3 2 2
4 0 0 3 0
```

这样我们便可以一行一行的计算，然后把每一行看做一个柱状图，数字代表柱子的高度，那么可以形成长方形的，就是连续的数字里取min，然后乘以下标的delta就行了。

代码如下：

```python
class Solution:
    def maximalRectangle(self, matrix):
        """
        :type matrix: List[List[str]]
        :rtype: int
        """
        if matrix is None or len(matrix) == 0:
            return 0
        m, n = len(matrix), len(matrix[0])
        for i in range(m):
            for j in range(n):
                matrix[i][j] = int(matrix[i][j])
        for i in range(1, m):
            for j in range(n):
                matrix[i][j] = matrix[i][j] + matrix[i - 1][j] if matrix[i][j] > 0 else 0
        area = 0
        print(matrix)
        for row in matrix:
            area = max(area, self.hist(row))
        return area

    
    def hist(self, hist):
        stack = []
        hist.append(0)
        area = 0
        for i in range(len(hist)):
            while len(stack) > 0 and hist[i] < hist[stack[-1]]:
                h = hist[stack.pop()]
                area = max(area, h * ((i - stack[-1] - 1) if len(stack) > 0 else i))
            stack.append(i)
        return area
            
```

