---
title: Insertion Sort List
tags: [Leetcode]

date: 2017-08-18
published: true
hideInList: false
feature: 
isTop: false
---







Sort a linked list using insertion sort.

**Solution**

[插入排序](https://en.wikipedia.org/wiki/Insertion_sort)

Python

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def insertionSortList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if head is None:
            return head
        dummy = ListNode(-1)
        dummy.next = head
        current = head
        while current:
            print(current.val)
            begin = dummy
            current_next = current.next
            if current_next and current_next.val < current.val:
                tmp_current = current
                current.next = current_next.next
                while begin != tmp_current:
                    if begin.next.val > current_next.val:
                        current_next.next = begin.next
                        begin.next = current_next
                        break
                    else:
                        begin = begin.next
            else:
                current = current.next
        return dummy.next
```

Java

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode insertionSortList(ListNode head) {
        ListNode helper = new ListNode(0);
        ListNode pre = helper;
        ListNode current = head;
        while(current!=null) {
            pre = helper;
            while(pre.next != null && pre.next.val < current.val) {
                pre = pre.next;
            }
            ListNode next = current.next;
            current.next = pre.next;
            pre.next = current;
            current = next;
        }
        return helper.next;
    }
}
```