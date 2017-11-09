---
title: Unique Binary Search Trees
date: 2017-11-08 22:03:56
tags:
- Tree
- Dynamic Programming
- BST

---

> Given *n*, how many structurally unique **BST's** (binary search trees) that store values 1...*n*?
>
> For example,
> Given *n* = 3, there are a total of 5 unique BST's.
>
> ```
>    1         3     3      2      1
>     \       /     /      / \      \
>      3     2     1      1   3      2
>     /     /       \                 \
>    2     1         2                 3
> ```



首先，我们都知道BST的规则，那么如果给出一组values：1 ~ n，如果我选取期中一个值，比如x作为根节点，那么我们可以很得出， 1 ~ x - 1 一定在左子树而 x + 1 ~ n 一定在右子树。那么假如左子树有 A 种构建方法，右子树有 B 种构建方法，那么构建一个基于 x 作为根节点的 BST就一定有 A * B 种方法。根据这样的分析，我们可以得知总的构建方法数量就是把 1 到 n 都做一次根节点，然后把结果累加就是最终的结果。但是同时我们也能意识到，这里面一定是有重复的。要计算 x + 1 ~ n 的右子树构建方法数量，就要做计算 x + 2 ~ n， x + 3 ~ n……计算x + 2 ~ n的时候一定需要计算 x + 3 ~ n。由此我们发现用DP可以避开重复计算从而加速。

综上，我们得出一个公式：如果用 `G(i)`来代表以 i 为root的所有构建方法的，那么`G(i) = G(i - 1) * G(n - i)`，i - 1 为左子树， n - i 为右子树。若要计算所有的 n ，那就是 `All(n) = G(1) + G(2) + G(3) + ... G(n)` = `G(0) * G(n - 1) + G(1) * G(n - 2) + G(3) * G(n - 3) + ... G(n - 1) * G(0)`。而base case就是 `G(0) = G(1) = 1`

代码如下:

```python
class Solution:
    def numTrees(self, n):
        """
        :type n: int
        :rtype: int
        """
        G = [0 for _ in range(n + 1)]
        G[0], G[1] = 1, 1
        for i in range(2, n + 1):
            for j in range(1, i + 1):
                G[i] += (G[j - 1] * G[i - j]) #G[i] = G[j - 1] * G[i - j] 0 < j <= i, sum of G(n) = G(0) * G(n-1) + G(1) * G(n-2) + … + G(n-1) * G(0) 
        return G[n]
```



这道题也有一个[follow up](https://leetcode.com/problems/unique-binary-search-trees-ii/description/)。就是构建所有的树，很明显，这样就不是一道DP题了。而是DFS。

但是根据以上的分析，我们直到要找到所有的tree，只需要把所有的结果作为root走一遍，然后把 i - 1 and n -  i 作为左右子树来使用，继续递归构建子树，然后把返回的左右子树做两两配对再返回上层即可。Base case为 如果 起始点大于终点，直接返回null，代表已到leaf。

代码如下

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def generateTrees(self, n):
        """
        :type n: int
        :rtype: List[TreeNode]
        """
        if n == 0:
            return []
        return self.build(1, n)
    def build(self, begin, end):
        res = []
        if (begin > end):
            res.append(None)
            return res
        for i in range(begin, end + 1):
            lefts = self.build(begin, i - 1)
            rights = self.build(i + 1, end)
            # res += [self.createTree(i, left, right) for left in lefts for right in rights]
            res += list(map(lambda pair: self.createTree(i, pair[0], pair[1]), itertools.product(lefts, rights)))
        return res
    def createTree(self, i, left, right):
        node = TreeNode(i)
        node.left = left
        node.right = right
        return node
```

