# [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

## Problem

Given the root of a binary search tree, and an integer k, return the Kth
smallest value (1-indexed) of all the values of the nodes in the tree.

## Solution

```rust
// Definition for a binary tree node.
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

use std::cell::RefCell;
use std::rc::Rc;

pub fn kth_smallest(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
    if let Some(root) = root {
        return inorder(root, &mut k.clone()).unwrap_or(0);
    }

    0
}

fn inorder(root: Rc<RefCell<TreeNode>>, n: &mut i32) -> Option<i32> {
    let root = root.borrow();
    if let Some(node) = root.left.clone() {
        if let Some(val) = inorder(node, n) {
            return Some(val);
        }
    }

    *n -= 1;
    if *n == 0 {
        return Some(root.val);
    }

    if let Some(node) = root.right.clone() {
        if let Some(val) = inorder(node, n) {
            return Some(val);
        }
    }

    None
}
```