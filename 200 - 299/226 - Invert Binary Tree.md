# [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)

## Problem

Given the root of a binary tree, invert the tree, and return its root.

#### Examples

```text
Input: root = [4,2,7,1,3,6,9]
Output: [4,7,2,9,6,3,1]

Original: 
4 -> 2, 7
2 -> 1, 3
7 -> 6, 9

Inverted:
4 -> 7, 2
7 -> 9, 6
2 -> 3, 1
```

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

pub fn invert_tree(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
    dfs(root.clone());
    root
}


fn dfs(root: Option<Rc<RefCell<TreeNode>>>) {
    if let Some(node) = root {
        let mut inner = node.borrow_mut();

        let left = inner.left.take();
        let right = inner.right.take();
        inner.right = left.clone();
        inner.left = right.clone();

        dfs(left);
        dfs(right);
    }
}
```