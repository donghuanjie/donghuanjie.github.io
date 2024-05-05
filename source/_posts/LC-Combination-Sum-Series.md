---
title: LC Combination Sum Series
date: 2024-01-24 01:14:18
categories:
  - [Leetcode]
tags:
  - Backtracking
  - Pruning
---

### Problems

#### [LC39: Combination Sum I](https://leetcode.cn/problems/combination-sum/description/)

In this problem, we do not have the limitation on using the single element multiple times, therefore we can use the backtracking algorithm and start next recursion from the index we are at each step. 

Note that we should not do the recursion on all the elements in candidate array because we don't want to output the same solution in any order `e.g. [2,3,3], [3,2,3], [3,3,2]`, therefore we should not **move backward** the index.

Note that the candidates array has only distinct elements, therefore we will not count same solution in different order multiple times because of the duplicates in the array. 

Also note that the candidates array is **not sorted** so we can't prune the solution after we find at any index the sum already exceeds the target but we should keep traverse the whole candidate array. And it reminds me of sorting the array at very first place.

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> ans = new ArrayList<>();
        dfs(ans, new LinkedList<Integer>(), candidates, target, 0);
        return ans;
    }

    public void dfs(List<List<Integer>> ans, List<Integer> temp, 
    int[] candidates,int remaining, int start) {
        // base case
        if (remaining == 0) {
            ans.add(new LinkedList<>(temp));
            return;
        } else if (remaining < 0) {
            return;
        }

        for (int i = start; i < candidates.length; i++) {
            temp.add(candidates[i]);
            dfs(ans, temp, candidates, remaining-candidates[i], i);
            temp.removeLast();
        }
    }
} 

// time: the time complextity is not fixed, O(n * 2^n) is the worst case where all the combinations are considered
// however, if we consider the search tree itself, it is O(S), S stands for the valid solutions tree node sum

// space: O(target) the longest valid target solution level
```

If we sort the array beforehand, then we can prune the search tree which would imporve our runtime overhead. Note that the time complexity won't change, but the algorithm is improved for sure.

```java
public void dfs(List<List<Integer>> ans, List<Integer> temp,
    int[] candidates, int remaining, int start) {
        if (remaining == 0) {
            ans.add(new ArrayList<>(temp));
        }

        for (int i = start; i < candidates.length; ++i) {
            if (remaining - candidates[i] < 0) break;  
            temp.add(candidates[i]);
            dfs(ans, temp, candidates, remaining-candidates[i], i);
            temp.removeLast();
        }
}

// we can sort the candidates array by using Arrays.sort(candidates) in the main function
// here we can check if candidates[i] exceeds the limit, if it does, we break and return to the previous level of the recursions
// it saves some overhead both in time and space but won't save the algotithm from the worst case
```

#### [LC40: Combination Sum II](https://leetcode.cn/problems/combination-sum-ii/description/)

From the previous problem, we found that `pruning` can only happens when the candidates array is sorted.





#### LC216: Combination Sum III

Find all valid combinations of k numbers that sum up to n such that the following conditions are true:

- Only numbers 1 through 9 are used.
- Each number is used at most once.

Return a list of all possible valid combinations. The list must not contain the same combination twice.

### Solution

We can use depth-first-search to search to the deepest element that is possible to sum up to the target and backtracking all the possible combinations. We loop from 1 to 9 and each time we add one single number to the sum and do the check. We recursively try every combinations

- Base case: if sum == target and the count of elements are equal to k, add the **NEW** temp list to the answer list

### Takeaways

- When we add the temp list to the answer, we should be aware of the ***reference*** copy here. 

```java
// add the reference to temp to the ans list
// this might have a problem when we delete something further in another level in recursion
ans.add(temp); 

// add the reference to a new list copied from temp list
ans.add(new ArrayList<>(temp));
```

- If we define the private answer list out side the main function, there might be safety problems. For example, if we have 2 thread sharing the same object, even if the ans list is private, there still be a thread safety problem.

```java
class Solution {
    private List<List<Integer>> ans = new ArrayList<>();

    public List<List<Integer>> combinationSum3(int k, int n) {
```

```java
Solution solution = new Solution();

// Thread A
new Thread(() -> solution.combinationSum3(k1, n1)).start();

// Thread B
new Thread(() -> solution.combinationSum3(k2, n2)).start();
```

- For `LinkedList`, `ArrayList` and `ArrayDeque` they are different:
`LinkedList` can do removeLast() directly, and add and delete from both head and tail are O(1)
`ArrayList` does not provide removeLast() method, but we can do remove(size() - 1) to similarly, add and delete at head is O(n)
`ArrayDeque` is dynamic double-ended queue, not as efficient as ArrayList in searching but provide all searching, inserting and deleting at O(1)