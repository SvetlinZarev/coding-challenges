# [222. Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

## Problem

Given the root of a complete binary tree, return the number of the nodes in the
tree.

According to Wikipedia, every level, except possibly the last, is completely
filled in a complete binary tree, and all nodes in the last level are as far
left as possible. It can have between `1` and `2h` nodes inclusive at the last
level `h`.

Design an algorithm that runs in less than `O(n)` time complexity.

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

use std::cell::RefCell;
use std::rc::Rc;

pub fn count_nodes(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let (left_height, right_height) = match &root {
        None => return 0,
        Some(node) => (lh(node.clone()), rh(node.clone())),
    };

    if left_height == right_height {
        return 2i32.pow(left_height + 1) - 1;
    }

    let root = root.as_ref().unwrap();
    1 + check_check_left(left_height - 1, root.borrow().left.clone())
        + count_nodes(root.borrow().right.clone())
}


fn check_check_left(left_height: u32, root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let right_height = match &root {
        None => return 0,
        Some(node) => rh(node.clone()),
    };

    if left_height == right_height {
        return 2i32.pow(left_height + 1) - 1;
    }

    if left_height == 0 {
        return 1;
    }

    let root = root.as_ref().unwrap();
    1 + check_check_left(left_height - 1, root.borrow().left.clone())
        + count_nodes(root.borrow().right.clone())
}

fn lh(root: Rc<RefCell<TreeNode>>) -> u32 {
    let mut node = root.borrow().left.clone();
    let mut height = 0;

    while let Some(inner) = node.clone() {
        node = inner.borrow().left.clone();
        height += 1;
    }

    height
}

fn rh(root: Rc<RefCell<TreeNode>>) -> u32 {
    let mut node = root.borrow().right.clone();
    let mut height = 0;

    while let Some(inner) = node.clone() {
        node = inner.borrow().right.clone();
        height += 1;
    }

    height
```