# [113. Path Sum II](https://leetcode.com/problems/path-sum-ii/)

## Problem

### Description

Given the root of a binary tree and an integer `targetSum`, return all **
root-to-leaf** paths where the sum of the node values in the path
equals `targetSum`. Each path should be returned as a list of the node values,
not node references.

A root-to-leaf path is a path starting from the root and ending at any leaf
node. A leaf is a node with no children.

### Constraints

* The number of nodes in the tree is in the range `[0, 5000]`.
* `-1000 <= Node.val <= 1000`
* `-1000 <= targetSum <= 1000`

### Examples

#### Example 1

![image](resources/113/ex1.jpg)

```text
Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: [[5,4,11,2],[5,8,4,5]]
Explanation: There are two paths whose sum equals targetSum:
5 + 4 + 11 + 2 = 22
5 + 8 + 4 + 5 = 22
```

#### Example 2

![image](resources/113/ex2.jpg)

```text
Input: root = [1,2,3], targetSum = 5
Output: []
```

#### Example 3

```text
Input: root = [1,2], targetSum = 0
Output: []
```

## Solutions

### DFS

```rust
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug, PartialEq, Eq)]
pub struct TreeNode {
    pub val: i32,
    pub left: Option<Rc<RefCell<TreeNode>>>,
    pub right: Option<Rc<RefCell<TreeNode>>>,
}

impl TreeNode {
    #[inline]
    pub fn new(val: i32) -> Self {
        TreeNode {
            val,
            left: None,
            right: None,
        }
    }
}

pub fn path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> Vec<Vec<i32>> {
    let mut answer = vec![];
    let mut path = vec![];

    if let Some(root) = &root {
        dfs(&mut answer, &mut path, root, target_sum, 0);
    }

    answer
}

fn dfs(
    answer: &mut Vec<Vec<i32>>,
    path: &mut Vec<i32>,
    root: &Rc<RefCell<TreeNode>>,
    target: i32,
    current: i32,
) {
    let root_ref = root.borrow();
    path.push(root_ref.val);

    let sum = current + root_ref.val;

    // Check if it's a leaf node or not
    if root_ref.left.is_none() && root_ref.right.is_none() {
        // If it's a leaf node and has the correct path sum, then
        // add the current path to the answer

        if target == sum {
            answer.push(path.clone());
        }
    } else {
        // If it's not a leaf node, DFS into its left/right subtrees

        if let Some(left) = root_ref.left.as_ref() {
            dfs(answer, path, left, target, sum);
        }

        if let Some(right) = root_ref.right.as_ref() {
            dfs(answer, path, right, target, sum);
        }
    }

    // remove the current node from the path
    path.pop();
}
```