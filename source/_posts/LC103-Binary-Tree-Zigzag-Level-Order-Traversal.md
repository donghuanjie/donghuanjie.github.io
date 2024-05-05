---
title: "LC103: Binary Tree Zigzag Level Order Traversal"
date: 2023-07-12 23:28:13
categories:
  - [Leetcode]
tags:
  - Binary Tree
  - Queue
  - Deque
---

### Previously

This problem is the more complex version of [LC102: Binary Tree Level Order Traversel](https://leetcode.com/problems/binary-tree-level-order-traversal/description/). In the previous version, we can only use a [Queue](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Queue.html) to implement a FIFO order traversal to solve the problem:

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }

        List<List<Integer>> ans = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            List<Integer> lst = new ArrayList<>();
            int size = queue.size();    // store how many nodes in each level
            while (size > 0) {
                TreeNode curr = queue.poll();
                lst.add(curr.val);
                if (curr.left != null) {
                    queue.add(curr.left);
                }
                if (curr.right != null) {
                    queue.add(curr.right);
                }
                size--;
            }
            ans.add(lst);
        }
        return ans;
    }
}

// time: O(n)
// space: O(n) (O(k) actually, k is for the most amount of nodes in each level, worst case n)
```

### Solution

For this problem where the zigzag traverse is required, we have 2 ways to solve it, either retrieve the nodes in a reversed order when level is odd (0 is the first level) or retrieve the node normally but reverse the list. It turns out that both can be done in the same time complexity. What's more, the first method can be implemented in 2 different ways as well.

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }

        List<List<Integer>> ans = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        boolean oddLevel = false; // 0 is first level

        while (!queue.isEmpty()) {
            List<Integer> lst = new LinkedList<>();
            int size = queue.size();
            while (size > 0) {
                TreeNode curr = queue.poll();
                if (oddLevel) {
                    lst.addFirst(curr.val);
                } else {
                    lst.add(curr.val); // default add is addLast
                }

                if (curr.left != null) {
                    queue.add(curr.left);
                }
                if (curr.right != null) {
                    queue.add(curr.right);
                }
                size--;
            }
            ans.add(lst);
            oddLevel = !oddLevel;
        }
        return ans;
    }
}

// time: O(n)
// space: O(n) (O(k) actually, same idea with above)
```

Here we can also use the  `Collections.reverse(List<E> list)` to reverse the List, but the time complexity for the reverse would be `O(n)` which will cause the total time to be `O(n^2)`.

### Takeaways

- [LinkedList](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedList.html) has method `addFirst(E)` and `addLast(E)` with default `add(E)` is `addLast(E)` and the time complexity both are `O(n)`.
- In Java, we can't directly get the node object if we are provided `List<E> lst` no matter it's linkedlist or arraylist.
- `Collections.reverse(List<E> list)` time complexity is `O(n)`.