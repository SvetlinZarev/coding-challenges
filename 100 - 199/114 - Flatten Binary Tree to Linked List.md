# [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

## Problem

Given the root of a binary tree, flatten the tree into a "linked list":

* The "linked list" should use the same TreeNode class where the right child
  pointer points to the next node in the list and the left child pointer is
  always `null`.
* The "linked list" should be in the same order as a pre-order traversal of the
  binary tree.

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

pub fn flatten(root: &mut Option<Rc<RefCell<TreeNode>>>) {
    if root.is_some() {
        let mut nodes = vec![];
        preorder(root, &mut nodes);

        let mut head = root.take().unwrap();
        let mut tail = head.clone();

        for mut node in nodes.drain(1..) {
            tail.borrow_mut().right.replace(node.clone());
            tail = node;
        }

        root.replace(head);
    }
}

fn preorder(root: &mut Option<Rc<RefCell<TreeNode>>>, nodes: &mut Vec<Rc<RefCell<TreeNode>>>) {
    if let Some(node) = root {
        nodes.push(node.clone());

        preorder(&mut node.borrow_mut().left.take(), nodes);
        preorder(&mut node.borrow_mut().right.take(), nodes);
    }
}
```