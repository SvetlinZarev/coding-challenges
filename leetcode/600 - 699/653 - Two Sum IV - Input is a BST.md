# [653. Two Sum IV - Input is a BST](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/)

## Problem

Given the root of a Binary Search Tree and a target number `k`, return `true` if
there exist two elements in the BST such that their sum is equal to the given
target.

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
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashSet;


impl Solution {
    pub fn find_target(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> bool {
        find_target(root, k)
    }
}


pub fn find_target(mut root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> bool {
    match root.take() {
        None => false,
        Some(rot) => {
            let mut values = HashSet::new();
            preorder(rot, &mut values, k)
        }
    }
}

fn preorder(root: Rc<RefCell<TreeNode>>, dst: &mut HashSet<i32>, k: i32) -> bool {
    let root = root.borrow();
    if dst.contains(&root.val) {
        return true;
    }

    dst.insert(k - root.val);

    if let Some(node) = root.left.clone() {
        if preorder(node, dst, k) {
            return true;
        }
    }

    if let Some(node) = root.right.clone() {
        if preorder(node, dst, k) {
            return true;
        }
    }

    false
}
```