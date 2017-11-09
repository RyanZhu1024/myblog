---
title: Edit Distance
date: 2017-11-08 21:57:44
tags:
- Array
- Dynamic Programming

---

> Given two words *word1* and *word2*, find the minimum number of steps required to convert *word1* to *word2*. (each operation is counted as 1 step.)
>
> You have the following 3 operations permitted on a word:
>
> a) Insert a character
> b) Delete a character
> c) Replace a character

这道题是典型的DP，因为想要找到最少的edit distance，如果用DFS一定很耗时。因此利用DP里一个典型的思路，前多个元素的修改建议 + 当前的修改建议，求最小，是可以得出最后结论的。说白了，就是要知道前 `i` 个 和 前 `j` 元素的distance，我们只需要通过比较 `i - 1, j - 1`, `i - 1, j` 和 `i, j - 1` 就可以了。他们分辨对应replace，add和delete。但是如果当前两个元素是一样的，那么就不需要任何修改，distance就直接来自于 ` i -1, j - 1`。

代码如下：

```python
class Solution:
    def minDistance(self, word1, word2):
        """
        :type word1: str
        :type word2: str
        :rtype: int
        """
        if word1 is None or word2 is None:
            return 0
        m, n = len(word1), len(word2)
        dp = [[0 for _ in range(n + 1)] for _ in range(m + 1)]
        for i in range(1, m + 1):
            dp[i][0] = dp[i - 1][0] + 1
        for i in range(1, n + 1):
            dp[0][i] = dp[0][i - 1] + 1
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                minStep = min([dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1]])
                dp[i][j] = dp[i - 1][j - 1] if word1[i - 1] == word2[j - 1] else (minStep + 1)
        return dp[m][n]
    
```

