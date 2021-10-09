# [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

## Problem

Given the root of a binary tree, return the inorder traversal of its nodes'
values.

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

pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut result = vec![];
    inorder(&mut result, root);
    result
}


fn inorder(path: &mut Vec<i32>, root: Option<Rc<RefCell<TreeNode>>>) {
    if let Some(node) = root {
        inorder(path, node.borrow().left.clone());
        path.push(node.borrow().val);
        inorder(path, node.borrow().right.clone());
    }
}

```