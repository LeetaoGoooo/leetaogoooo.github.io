---
title: Merge Intervals
tags: [Leetcode]

date: 2017-08-21
published: true
hideInList: false
feature: 
isTop: false
---







Given a collection of intervals, merge all overlapping intervals.

For example,
Given <code>[1,3],[2,6],[8,10],[15,18]</code>,
return <code>[1,6],[8,10],[15,18]</code>.

**Solution**

两个区间(A,B)能合并,说明这 <code>A.end >= B.start and A.start <= B.start </code>,将这些区间以 **start** 的值从小到大排序,
如果两个区间有重叠则合并这个区间,否则直接将该区间存入结果中


Python 

```python
# Definition for an interval.
# class Interval(object):
#     def __init__(self, s=0, e=0):
#         self.start = s
#         self.end = e

class Solution(object):
    def merge(self, intervals):
        """
        :type intervals: List[Interval]
        :rtype: List[Interval]
        """
        res = []
        for i in sorted(intervals, key=lambda i: i.start):
            if res and i.start <= res[-1].end:
                res[-1].end = max(res[-1].end, i.end)
            else:
                res.append(i)
        return res
```


Java

```java

// 来自讨论区

public List<Interval> merge(List<Interval> intervals) {
    if (intervals.size() <= 1)
        return intervals;
    
    // Sort by ascending starting point using an anonymous Comparator
    intervals.sort((i1, i2) -> Integer.compare(i1.start, i2.start));
    
    List<Interval> result = new LinkedList<Interval>();
    int start = intervals.get(0).start;
    int end = intervals.get(0).end;
    
    for (Interval interval : intervals) {
        if (interval.start <= end) // Overlapping intervals, move the end if needed
            end = Math.max(end, interval.end);
        else {                     // Disjoint intervals, add the previous one and reset bounds
            result.add(new Interval(start, end));
            start = interval.start;
            end = interval.end;
        }
    }
    
    // Add the last interval
    result.add(new Interval(start, end));
    return result;
}
```