# [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

## Problem

Given the root of a binary tree, return the postorder traversal of its nodes'
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
use std::rc::Rc;
use std::cell::RefCell;


pub fn postorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut nodes = vec![];
    postorder(root, &mut nodes);
    nodes
}


fn postorder(root: Option<Rc<RefCell<TreeNode>>>, nodes: &mut Vec<i32>) {
    if let Some(node) = root {
        postorder(node.borrow().left.clone(), nodes);
        postorder(node.borrow().right.clone(), nodes);
        nodes.push(node.borrow().val);
    }
}
```