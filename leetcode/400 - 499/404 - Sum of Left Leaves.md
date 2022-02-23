# [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)

## Problem

Given the root of a binary tree, return the sum of all left leaves.

##### Constraints

* The number of nodes in the tree is in the range `[1, 1000]`
* `-1000 <= Node.val <= 1000`

#### Examples

```text
Input: root = [3,9,20,null,null,15,7]
Output: 24
Explanation: There are two left leaves in the binary tree, with values 9 and 15 respectively.
```

```text
Input: root = [1]
Output: 0
Explanation: the root node is not a "left" leaf
```

## Solution

```rust
use std::cell::RefCell;
use std::rc::Rc;

// the tree definition from LeetCode
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

pub fn sum_of_left_leaves(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    match root {
        None => 0,
        Some(root) => dfs(root, false),
    }
}

fn dfs(node: Rc<RefCell<TreeNode>>, left: bool) -> i32 {
    let node = node.borrow();
    if left && node.left.is_none() && node.right.is_none() {
        return node.val;
    }

    let mut sum = 0;
    if let Some(child) = node.left.clone() {
        sum += dfs(child, true);
    }
    if let Some(child) = node.right.clone() {
        sum += dfs(child, false);
    }
    sum
}
```
