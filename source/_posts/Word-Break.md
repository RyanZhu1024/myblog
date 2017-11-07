---
title: Word Break
date: 2017-11-06 22:05:33
tags:
- Dynamic Programming
- Array

---

1. [Medium](https://leetcode.com/problems/word-break/description/)
2. [Hard](https://leetcode.com/problems/word-break-ii/description/)

这是一道典型的DP题，而且它的follow up是是一个DFS + DP，比较复杂，很考验基本功。我们一个一个来。

### Meduim

> Given a **non-empty** string *s* and a dictionary *wordDict* containing a list of **non-empty** words, determine if *s* can be segmented into a space-separated sequence of one or more dictionary words. You may assume the dictionary does not contain duplicate words.
>
> For example, given
> *s* = `"leetcode"`,
> *dict* = `["leet", "code"]`.
>
> Return true because `"leetcode"` can be segmented as `"leet code"`.

初看此题，可用DFS解之，毕竟只要穷举所有可能的break position就可以知道了，但是这也明显是很低效的。那么，经验是DFS慢的，加个Memory变成DP就可以解决了。

此题属于DP里面那种『前几个items怎样怎样』的，意思就是，我们需要通过数组来存储关于前 K 个元素的结果，而非到第 K 个元素的结果。例如题目中的例子，判定leetcode是否breakable，我们可以看到假如leet是已经breakable的了，那么只要code也在dict里面就可以了。公式化，就是：`dp[i] = dp[j] and s[j : i] in wordDict` where `j >= 0 and j >= maxLen(wordDict)`。大于0可以理解，那为何还要大于 `maxLen(wordDict)`呢？因为其实我们在遍历`s`的时候并不需要遍历完整个`s`，而只需要遍历到能够break的最远距离就好了，超出那个距离，你想break也没有word给你break了。那么，至此分析完毕，代码：

```python
class Solution:
    def wordBreak(self, s, wordDict):
        """
        :type s: str
        :type wordDict: List[str]
        :rtype: bool
        """
        if s is None or wordDict is None or len(s) == 0 or len(wordDict) == 0:
            return False
        dp = [False for _ in range(len(s) + 1)]
        dp[0] = True
        maxLen = max(map(lambda word: len(word), wordDict))
        for i in range(1, len(s) + 1):
            for j in range(i - 1, max(0, i - maxLen) - 1, -1):
                dp[i] = dp[i] or (dp[j] and (s[j:i] in wordDict))
                if dp[i]:
                    break
        return dp[len(s)]
```

### Hard

那么这道Hard的follow up怎么做呢？其实只需要对之前的代码做点修改加上DFS搜索就可以了。修改如下：

1. 不存True和False，而是存下当前position下一个position的位置，例如leetcode里面，t所在位置应该存下e的位置，意思就是从t开始下一个在e。
2. 把False改成Null，这样就可以识别出，这个position是无法break的
3. DFS就是遍历整个DP vector和每个元素列表里指向的下个position，跳转至这个position，做递归继续调用，直到position指向了`s`的终点。
4. 一个注意点，就是如果DP vector的最后一个元素是Null，代表`s`是无法break的，直接返回空列表即可。

代码：

```python
class Solution:
    def wordBreak(self, s, wordDict):
        """
        :type s: str
        :type wordDict: List[str]
        :rtype: List[str]
        """
        if s is None or wordDict is None or len(s) == 0 or len(wordDict) == 0:
            return []
        maxLen = max([len(word) for word in wordDict])
        breakPoints = self.getBreakPoints(s, wordDict, maxLen)
        res = []
        if breakPoints[len(s)] is not None:
            self.searchCombinations(breakPoints, s, res, 0, "")
        return res
    
    def getBreakPoints(self, s, wordDict, maxLen):
        points = [None for _ in range(len(s) + 1)]
        points[0] = []
        for i in range(1, len(s) + 1):
            for j in range(i - 1, (max(0, i - maxLen ) - 1), -1):
                if points[j] is not None and s[j:i] in wordDict:
                    points[j].append(i)
                    if points[i] == None:
                        points[i] = []
        print(points)
        return points
    
    def searchCombinations(self, points, s, res, begin, current):
        if begin == len(s):
            res.append(current.strip())
        for position in points[begin]:
            self.searchCombinations(points, s, res, position, current + s[begin:position] + " ")
```

