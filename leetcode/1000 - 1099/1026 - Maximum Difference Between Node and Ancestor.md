# [1026. Maximum Difference Between Node and Ancestor](https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/)

## Problem

Given the root of a binary tree, find the maximum value `v` for which there
exist different nodes `a` and `b` where `v = |a.val - b.val|` and `a` is an
ancestor of `b`.

A node `a` is an ancestor of `b` if either:

* any child of `a` is equal to `b`
* or any child of `a` is an ancestor of `b`.

#### Constraints

* The number of nodes in the tree is in the range `[2, 5000]`.
* `0 <= Node.val <= 10^5`

#### Examples

```text
Input: root = [1,null,2,null,0,3]
Output: 3
```

```text
Input: root = [8,3,10,1,6,null,14,null,null,4,7,13]
Output: 7
Explanation: We have various ancestor-node differences, some of which are given below :
|8 - 3| = 5
|3 - 7| = 4
|8 - 1| = 7
|10 - 13| = 3
Among all possible differences, the maximum value of 7 is obtained by |8 - 1| = 7.
```

## Solution

#### Ideas

The "maximum difference" means that we have to find the largest/smallest nodes
that have a parent-child relationship - i.e. they are on the same subtree.

### Recursive bottom-up approach

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

pub fn max_ancestor_diff(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    match root {
        None => 0,
        Some(root) => dfs(root).0,
    }
}

// (best, min, max)
fn dfs(root: Rc<RefCell<TreeNode>>) -> (i32, i32, i32) {
    let root = root.borrow();

    // If the current node is a leaf node, then use its value as the initial min/max
    if root.left.is_none() && root.right.is_none() {
        return (0, root.val, root.val);
    }

    let (mut best, mut min, mut max) = (0, i32::MAX, i32::MIN);

    // find the max_diff/min/max in the left subtree
    if let Some(node) = root.left.clone() {
        let (b, mi, ma) = dfs(node);
        best = best.max(b);
        min = min.min(mi);
        max = max.max(ma);
    }

    // find the max_diff/min/max in the right subtree
    if let Some(node) = root.right.clone() {
        let (b, mi, ma) = dfs(node);
        best = best.max(b);
        min = min.min(mi);
        max = max.max(ma);
    }

    // the current best value is either the previous best value
    // or the current node - subtree's min/max
    let a = (root.val - min).abs();
    let b = (root.val - max).abs();
    best = best.max(a).max(b);

    (best, min.min(root.val), max.max(root.val))
}
```

### Recursive top-down approach

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

pub fn max_ancestor_diff(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    match root {
        None => 0,
        Some(root) => dfs(root, i32::MAX, i32::MIN),
    }
}

fn dfs(root: Rc<RefCell<TreeNode>>, min: i32, max: i32) -> i32 {
    let root = root.borrow();
    let min = min.min(root.val);
    let max = max.max(root.val);

    if root.left.is_none() && root.right.is_none() {
        return max - min;
    }

    let mut max_diff = 0;

    if let Some(node) = root.left.clone() {
        max_diff = dfs(node, min, max);
    }

    if let Some(node) = root.right.clone() {
        max_diff = max_diff.max(dfs(node, min, max));
    }

    max_diff
}
```