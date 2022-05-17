# [1379. Find a Corresponding Node of a Binary Tree in a Clone of That Tree](https://leetcode.com/problems/find-a-corresponding-node-of-a-binary-tree-in-a-clone-of-that-tree/)

## Problem

### Description

Given two binary trees `original` and `cloned` and given a reference to a
node `target` in the `original` tree.

The `cloned` tree is a copy of the `original` tree.

Return a reference to the same node in the `cloned` tree.

Note that you are not allowed to change any of the two trees or the `target`
node and the answer must be a reference to a node in the `cloned` tree.

### Follow-up

Could you solve the problem if repeated values on the tree are allowed?

### Constraints

* The number of nodes in the `tree` is in the range `[1, 10^4]`.
* The values of the nodes of the `tree` are unique.
* `target` node is a node from the `original` tree and is not `null`.

### Examples

#### Example 1

![image](resources/1379/ex1.png)

```text
Input: tree = [7,4,3,null,null,6,19], target = 3
Output: 3
Explanation: In all examples the original and cloned trees are shown. The target node is a green node from the original tree. The answer is the yellow node from the cloned tree.
```

#### Example 2

![image](resources/1379/ex2.png)

```text
Input: tree = [7], target =  7
Output: 7
```

#### Example 3

![image](resources/1379/ex3.png)

```text
Input: tree = [8,null,6,null,5,null,4,null,3,null,2,null,1], target = 4
Output: 4
```

## Solutions

#### LeetCode API

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
    }
}
```

### Pre-order DFS

```java
class Solution {
    public final TreeNode getTargetCopy(final TreeNode original, final TreeNode cloned, final TreeNode target) {
        if (original == target) {
            return cloned;
        }

        if (null != original.left) {
            final var answer = getTargetCopy(original.left, cloned.left, target);
            if (null != answer) {
                return answer;
            }
        }

        if (null != original.right) {
            final var answer = getTargetCopy(original.right, cloned.right, target);
            if (null != answer) {
                return answer;
            }
        }
        
        return null;
    }
}
```
