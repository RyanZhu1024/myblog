---
title: Task Scheduler
date: 2017-11-03 18:04:54
tags:
- Greedy
- PriorityQueue

---

# [Go to problem](https://leetcode.com/problems/task-scheduler/description/)

Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval **n** that means between two **same tasks**, there must be at least n intervals that CPU are doing different tasks or just be idle.

You need to return the **least** number of intervals the CPU will take to finish all the given tasks.

**Example 1:**

```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.
```

------

### 解释

他废话那么多，其实就是要表达，你有一个 Job 序列，每个 Job都要被CPU执行，消费 1 个 unit 的时间，相同两个 Job 之间必须空出 n 个 unit 时间，即

> 序列： AAA，n = 2， 那么你不能按照AAA执行，必须是 A _ _ A _ _ A

并且，最终的 Job 执行序列是无所谓的。求解最短需要多少个 unit 时间

------

### 分析

这道题解法其实是贪心。然而贪心一般证明起来很困难，因此我觉得可以通过common sense来解释，请看例子如下：

> ABC -> ABC

因为ABC非同类 Job， 他们的执行无需任何冷却

> AAC -> A C _ A.  4 units 
>
> Not C A _ _ A.    5 units

有两个同类 Job， 我们选择先执行多的那个，然后执行少的，再执行多的

> AAABBC -> ABCAB_A. 7 units
>
> AAABBC -> CBA _ B _ A _ _ A. 10 units
>
> BCAB _ A _  _ A 9 units

理由同上

可以看出，只要把最frequent的 Job 先执行了，之后执行第二frequent，以此类推，那么就可以解出答案。

由此得到思路：Job以frequency来排序，一次性取出前 K 个 Job，执行完，更新frequency，再取前 K 个。那如果取不出 K 个呢？就等待，等待多久？如果取走 x 个Job，那么就等待 K - x 个units

以下是 Code：

```python
from queue import PriorityQueue
from collections import Counter

class TaskItem:
    def __init__(self, task, count):
        self.task = task
        self.count = count
        
    def __lt__(self, other):
        if self.count == other.count:
            return self.task > other.task
        return self.count > other.count


class Solution:
    def leastInterval(self, tasks, n):
        """
        :type tasks: List[str]
        :type n: int
        :rtype: int
        """
        q = PriorityQueue()
        for item in Counter(tasks).items():
            q.put(TaskItem(item[0], item[1]))
        timetable = {}
        result = 0
        while not q.empty():
            k = n + 1
            tempList = []
            while not q.empty() and k > 0:
                taskItem = q.get()
                taskItem.count = taskItem.count - 1
                k -= 1
                tempList.append(taskItem)
                result += 1
            for t in tempList:
                if t.count > 0:
                    q.put(t)
            if q.empty():
                break
            result += k
        return result                
```

