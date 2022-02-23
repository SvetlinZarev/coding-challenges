# [111. Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

## Problem

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root
node down to the nearest leaf node.

**Note:** A leaf is a node with no children.

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
use std::collections::VecDeque;
use std::rc::Rc;

pub fn min_depth(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    bfs(root)
}


fn bfs(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut nodes = VecDeque::new();
    if let Some(node) = root {
        nodes.push_back((node, 1));
    }

    while let Some((node, depth)) = nodes.pop_front() {
        let mut has_sub_tree = false;

        if let Some(left) = node.borrow_mut().left.take() {
            has_sub_tree = true;
            nodes.push_back((left, depth + 1));
        }

        if let Some(right) = node.borrow_mut().right.take() {
            has_sub_tree = true;
            nodes.push_back((right, depth + 1));
        }

        if !has_sub_tree {
            return depth;
        }
    }

    0
}
```