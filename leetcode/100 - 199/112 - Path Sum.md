# [112. Path Sum](https://leetcode.com/problems/path-sum/)

## Problem

Given the root of a binary tree and an integer `targetSum`, return `true` if the
tree has a **root-to-leaf** path such that adding up all the values along the
path equals `targetSum`.

A leaf is a node with no children.

## Solution

```rust
// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//   pub val: i32,
//   pub left: Option<Rc<RefCell<TreeNode>>>,
//   pub right: Option<Rc<RefCell<TreeNode>>>,
// }
// 
// impl TreeNode {
//   #[inline]
//   pub fn new(val: i32) -> Self {
//     TreeNode {
//       val,
//       left: None,
//       right: None
//     }
//   }
// }
use std::rc::Rc;
use std::cell::RefCell;


pub fn has_path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> bool {
    match root {
        None => false,
        Some(root) => dfs(root, target_sum, 0),
    }
}

fn dfs(root: Rc<RefCell<TreeNode>>, target: i32, mut current: i32) -> bool {
    let root = root.borrow();
    current += root.val;

    if root.left.is_none() && root.right.is_none() {
        if current == target {
            return true;
        }
    }

    if let Some(node) = root.left.clone() {
        if dfs(node, target, current) {
            return true;
        }
    }

    if let Some(node) = root.right.clone() {
        if dfs(node, target, current) {
            return true;
        }
    }

    return false;
}
```