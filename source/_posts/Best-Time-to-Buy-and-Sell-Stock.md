---
title: Best Time to Buy and Sell Stock
date: 2017-11-03 17:47:59
tags: 
- Algorithm
- Array
- preSum
- Dynamic Programming
---



这是一个系列的问题，但是大致的思路是一致的，下面将罗列出他的四个变种以及他们的解题思路和代码

## [Only one transaction](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

> Say you have an array for which the *i*th element is the price of a given stock on day *i*.
>
> If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

第一种最简单的要求，只能完成一次交易，那么搬脚趾头都能想出来，只要从左到右，寻找最小的购买价格，计算每次遍历元素给出的最大利润，即 `maxProfit = max(maxProfit, currentPrice  - minBuy) for currentPrice and minBuy from 1 to n, minBuy will be initialized with prices[0]`。 这个算法其实有一个assumption，即从左到右的情况下可以得出`minBuy`，那如果从右到左遍历是不是也能解题呢？

以下给出代码

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if prices is None or len(prices) < 2:
            return 0
        profit, i, localMin = 0, 1, prices[0]
        while i < len(prices):
            profit = max(profit, prices[i] - localMin)
            localMin = prices[i] if prices[i] < localMin else localMin
            i += 1
        return profit
```

## [As many transactions as you want](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

> Say you have an array for which the *i*th element is the price of a given stock on day *i*.
>
> Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

这个变种题把一次交易变成了无上限交易，其他一致。有时候我们的思路会被算法所限制，这道题的神奇之处在于，买卖股票是个现实例子，因此我们完全可以撇开算法看现实。现实中如果给你这么一撮股票价格，你怎么保证自己赚的金玉满钵呢？**见到有利润就卖掉就行了**。但这里要明白一件事情，即描述中所说的，**However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again**, 这里并不是说你真的不能在一天里进行多次交易，而是你必须卖了股票才能买入。比如，价格如下：3，4，5，最大利润自然是2，根据我们的描述，2 = 4 - 3 + 5 - 4，但其实也等于5 - 3，原因是，4和4从等式中抵消了，也就是说，你在第二天卖出然后又买入，和直接进行第一和第三天的价格交易是等价的，所以你既可以通过以小博大的方式见到利润就交易，也可以通过搜索递增序列，做头尾相减。

以下给出代码:

```python
class Solution(object):
    def maxProfit(self, prices):
        if prices is None or len(prices) < 2:
            return 0
        profit, i = 0, 0
        for i in range(len(prices) - 1):
            if prices[i + 1] > prices[i]:
                profit += (prices[i + 1] - prices[i])
        return profit    
```

## Complete k times transaction

这个变种就难了，因为你也不知道k是多少，k应该如何分布让自己利润最大化。那么首先我们先看看如果k=2的情况。

### [k = 2](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

我们知道，在k=1的时候只要找到最大和最小的有序组就可以了，那么k=2可以理解成，只要找到3个或者4个元素的有序组，让他们的差和最大就可以了。

换一种说法，在k=1的时候，我们需要找到一个全局的pair，让他们的差最大；k=2时，我们要找两个局部最大，让他们各自的差之和最大。那么如何找到局部和最大呢？所谓局部最大，就是说，在一个子集合中，找他们的最大pair；换句话说，如果prices的长度为10，我们需要知道k=2，3，4，5，6，7，8，9的最大利润值，当我们知道了前2，3，4，5，6，7，8，9的最大利润值后，我们还需要知道后2，3，4，5，6，7，8，9的最大利润值，即需要计算以下range的最大利润值（1，2），（1，3），（1，4）.......（1，10）和（9，10），（8，10），（7，10）….（1，10）。前一组很好记算，那么后一组呢？其实也不难，只需逆向遍历，replace minBuy with maxSell即可

以下为代码：

```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if prices == None or len(prices) < 2:
            return 0
        deltaRow = [0 for _ in range(len(prices))]
        # left to right
        minBuy = prices[0]
        for i in range(1, len(prices)):
            deltaRow[i] = max(deltaRow[i - 1], prices[i] - minBuy)
            minBuy = min(prices[i], minBuy)
        # right to left
        print(deltaRow)
        maxSell = prices[len(prices) - 1]
        for i in range(len(prices) - 2, -1, -1):
            deltaRow[i] = max(deltaRow[i], deltaRow[i] + maxSell - prices[i])
            maxSell = max(prices[i], maxSell)
        print(deltaRow)
        return max(deltaRow)
```

### [k > 2](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

但是以上做法在当k>2的时候是无法胜任了。但是它确实给我提供了一中思路，叫preSum，即前几个的结果。这也是在大部分Dynamic Programming里面所使用的思路

在k>2的时候我们需要更换我们的思路，即仔细想想，如果只能执行k次，那么如果知道k - 1次的结果是不是可以帮助我们得到k次的结果呢？

从上面的k=2我们可以看出，其实我们所保存的deltaRow就是在k=1的时候结果，然后再逆向的去搜索`maxSell`， 然后使用这个公式`deltaRow[i] = max(deltaRow[i], deltaRow[i] + maxSell - prices[i])`, 代表：最大值无非是这一天我不执行额外交易`deltaRow[i]`或者执行交易`deltaRow[i] + maxSell - prices[i]`，这里只需要track这个`maxSell`就可以了。而maxSell就是这一天的价格，因为我们遍历这一天即认为这一天也许会有交易。

那么，把它generalize一下，当k>2的时候，我就需要加上一个维度来代表k，也就是`deltsRow[k][i]`， 那么用同样的思路来公式化一下：`deltaRow[k][i] = max(deltaRow[k - 1][i], deltaRow[k][j - 1] + prices[i] - prices[j])`， 代表第`i`天的前`k`次交易，等于，这一天不执行交易的结果，即第`i`天已经执行了`k`次交易*（可能你会想为何不是`k-1`，其实正如在上一个section里所描述，我在同一天里买进再卖出，等于没买，而这一次无意义的交易可以看做是第`k`次）*，或者第`i` 天执行了`k - 1`次交易，然后在需要在前 i 天里面寻找一次可以与当前`prices[i]`给出最大利润的那个` j`。 （0 < j < i）

那么，换句话说，`f[k][i] = max(f[k][i - 1], p[i] - p[j] + f[k - 1][j])` 公式里面需要pay attention的就是`p[i] - p[j] + f[k - 1][j])`，这个简化下就是去`maximize (f[k - 1][j] - p[j])`, where `j` grows as `i` grows because` j` is less than `i`. 所以我们可以一边遍历`i`，一边track这个变量，而无需额外的一次遍历，`j` 初始化为`0`。如果不是特别清楚为何`j`无需额外一次遍历，请看代码中`j`的变化。下面给出一部分例子

> i = 3, 0 <= j < i
>
> 根据我们的策略，需要遍历每一次 `i` 寻找最佳组合，可以有如下 `i`和`j`的变化过程
>
> i = 1; j = 0
>
> i = 2; j = 0, 1
>
> i = 3, j = 0, 1, 2
>
> 那为何一个tmpMax就解决问题了呢？
>
> 因为对于每个`k`, 这个`tmpMax`是一个随 `i` 的变化而最终定位到当前 `k` 的最大值处的。例如在 `k = 2`里面，我们其实就是要找到一个切分点从而让左半部分和右半部分可以给出一个最大和。既然 `tmpMax`相对于每个`k` 是个常量，那么对于当前的`k`，无需另外的搜索，只需track其之前的值，求max即可

但是当 `K >= N / 2`的时候，就变成了见缝插针了（As many as you want），所以以上分析仅适用于 `K < N / 2`

以下为代码：

```python
class Solution(object):
    def maxProfit(self, prices, k):
        """
        :type prices: List[int]
        :rtype: int
        """
        if prices == None or len(prices) < 2:
            return 0
        # f[k][i] = max(f[k][i - 1], p[i] - p[j] + f[k - 1][j])
        n = len(prices)
		if (k > (n >> 1)):
            return self.quick(prices)
        dp = [[0 for _ in range(n)] for _ in range(k + 1)]
        maxProf = 0
        for k in range(1, k + 1):
            tmpMax = dp[k - 1][0] - prices[0] # j here is i since j grows with i
            for i in range(1, n):
                dp[k][i] = max(dp[k][i - 1], prices[i] + tmpMax)
                tmpMax = max(tmpMax, dp[k - 1][i] - prices[i])
                maxProf = max(dp[k][i], maxProf)
        return maxProf
    
    def quick(self, prices):
        n = len(prices)
        profit = 0
        for i in range(1, n):
            profit += max(0, prices[i] - prices[i - 1])
        return profit    
        
```

## 总结

从这道题可以看出，同一题型，变个问法就会变得很难，但是思路却是值得学习，并且在之后的blogs里，会慢慢发现，preSum是个非常有用的思路，尤其在DP问题里。

最后总结下这个系列的问题本质，根据我的理解，这道题本质就是求解一个数组中，通过搜索得出 `k` 组有序二元组（**有序二元组指的是两个元素在数组的相对位置不发生改变**），使其求和之后得到最大值。因此：

- K = 0

  - 就是0

- K = 1

  - 一个有序二元组，差的绝对值最大。搜索过程只需通过从左向右，track一个最小值，并用每一个当前迭代到的值去做减法，求的全局最大。
  - 这里的最小值，即局部最小，即当前`i`所到之处的最小。

- 1 < K < N / 2

  - 多个有序二元组，差的绝对值总和最大。如果做brute force，那将会是从 C(n, 2)个二元组里搜索出 K 个，使其总和最大。
  - 由于无需知道是哪些二元组而只需求出最大值，那么思路转变下，我们知道 `K = 1` 的时候很容易，那么扩展出来，假如我们已经知道了 `K - 1` 时的每个局部最大值（前 `i` 个），那么求 `K` 就很容易了，只需遍历从头遍历，用 `K = 1` 的方式求解最大值并且加上 `(K - 1, i)`或者 `(K - 1, i - 1)`的值，就是最大值，这里为何有 `i - 1` ，因为我们可以不做交易。
  - 因此，公式便可写成： `f[k][i] = max(f[k][i - 1], p[i] - p[j] + f[k - 1][j])`， `f[k - 1][j] - p[j]` 可以看做是preMax，即当前 `i` 的preMax，也就是到当前 `i` 为止，`k - 1`的最大收益。

- K >= N / 2

  - 见缝插针即可（As many as you want）

  ​

  ​