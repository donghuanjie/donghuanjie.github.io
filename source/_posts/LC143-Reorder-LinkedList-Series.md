---
title: 'LC143: Reorder LinkedList'
date: 2024-03-06 12:16:07
categories:
  - [Leetcode]
tags:
  - LinkedList
  - Recursion
---

### Previously

Some other problems are involved, for example:
- [LC206: Reverse LinkedList](https://leetcode.com/problems/reverse-linked-list/description/)
- [LC876: Middle of LinkedList](https://leetcode.com/problems/middle-of-the-linked-list/description/)

### Problems

#### LC143

{% img /images/LinkedList/lc143.png 800 700 '"problem" "alt"' %}

### Solution

To make it work, we should find the middle node and split the linkedlist into 2 halves. And then we reverse the second half and get the new head (which was the tail). Finally we merge 2 lists.

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) {
            return;
        }

        ListNode midNode = findMid(head);
        ListNode midNext = midNode.next;
        midNode.next = null;
        ListNode revHead = reverseList(midNext);
        head = mergeList(head, revHead);
    }

    // findMid method uses 2 pointers and runs through the list once O(n)
    public ListNode findMid(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    // reverseList runs throught the list once O(n)
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        ListNode next = curr.next;
        while (next != null) {
            curr.next = prev;
            prev = curr;
            curr = next;
            next = next.next;
        }
        curr.next = prev;
        return curr;
    }

    // mergeList merges 2 half list and takes O(n)
    public ListNode mergeList(ListNode one, ListNode two) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (two != null) {
            curr.next = one;
            one = one.next;
            curr.next.next = two;
            two = two.next;
            curr = curr.next.next;
        }
        if (one != null) {
            curr.next = one;
        }
        return dummy.next;
    }
}

// time: O(n)
// space: O(1)
```

### Takeaways

- Don't forget to remove the connection between the first half and second half of the linkedlist `midNode.next = null`, if we forget to remove it, the linkedList will have a cycle.
- The second half will always `second.size() <= first.size()` because we choose `ListNode midNext = midNode.next`, therefore when we merge 2 lists together, we can only check `while (two != null)`.
- Dummy node is useful here, it makes the code uniform in each step (otherwise the first step is different from the following steps).