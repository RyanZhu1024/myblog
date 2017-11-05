---
title: Regular Expression Matching
date: 2017-11-04 16:58:38
tags:
- Dynamic Programming
- Array
---

[Go here](https://leetcode.com/problems/regular-expression-matching/description/)

Implement regular expression matching with support for `'.'` and `'*'`.

```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "a*") → true
isMatch("aa", ".*") → true
isMatch("ab", ".*") → true
isMatch("aab", "c*a*b") → true
```



正则匹配，只需实现 `.` 和 `*` 就可以了，两者分别代表匹配任意一个字符和匹配0到n个前字符。如果不用DP，这道题看上去就特别复杂。那么我就思考如何用DP。

既然是DP，那肯定得有表，我们得定义表的含义。这道题目的是匹配两个字符串，一个source，一个pattern。那么根据一般DP的思路，如果要匹配source和pattern，那么就必须得保证前置substring匹配，然后再看当前字符是否匹配。那么我们可以这样想，用 m 和 n 代表两者长度， i 和 j分辨作为遍历两个字符串的下标，那么可以分析得出如下几个case：

1. `source[i] == pattern[j]`，这种情况下，只需要`source[i - 1] 和 pattern [j - 1]`也匹配就好了，不然就是False
2. `pattern[j] == '.'`，这种情况和上面一样
3. `patter[j] == '*'`，这情况就比较复杂了。我们分成如下几个subcase来分析
   1. `*` 匹配前面一个或者多个source子字符串。这种情况需要查看 `source[i] == pattern[j - 1]` or `pattern[j - 1] == '.'`
   2. `*` 匹配0个字符串。直接查看 `source[i]` and `pattern[j - 2]`

最后，我们用一个DP table来track前 i ，j 的匹配情况即可。

代码：

```python
class Solution:
    def isMatch(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: bool
        """
        if s == None or p == None:
            return False
        m, n = len(s), len(p)
        dp = [[False for _ in range(n + 1)]for _ in range(m + 1)]
        dp[0][0] = True
        for i in range(1, n + 1):
            if p[i - 1] == '*':
                dp[0][i] = dp[0][i - 2] if i > 1 else True
        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == '.' or p[j - 1] == s[i - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                elif p[j - 1] == '*' and j > 1:
                    dp[i][j] = dp[i][j - 2] or (dp[i - 1][j] and (s[i - 1] == p[j - 2] or p[j - 2] == '.'))
        return dp[m][n]
```

