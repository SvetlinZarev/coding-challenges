# [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

## Problem

Given the root of a binary tree, return the level order traversal of its nodes'
values. (i.e., from left to right, level by level).

#### Examples

```text
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]
```

## Solutions

### BFS

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

pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
    let mut r = vec![];
    let mut a = vec![];
    let mut b = vec![];

    a.push(root);
    while !a.is_empty() {
        b.clear();

        let mut group = vec![];
        for n in a.iter() {
            if let Some(node) = n {
                let node = node.borrow();
                group.push(node.val);
                if node.left.is_some() {
                    b.push(node.left.clone());
                }
                if node.right.is_some() {
                    b.push(node.right.clone());
                }
            }
        }

        if !group.is_empty() {
            r.push(group);
        }

        std::mem::swap(&mut a, &mut b);
    }

    r
}
```

### DFS

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

pub fn level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
    let mut result = vec![];
    if let Some(root) = root {
        dfs(&mut result, root, 0);
    }
    result
}

fn dfs(r: &mut Vec<Vec<i32>>, root: Rc<RefCell<TreeNode>>, level: usize) {
    if level >= r.len() {
        r.push(vec![]);
    }

    r[level].push(root.borrow().val);
    if let Some(node) = root.borrow().left.clone() {
        dfs(r, node, level + 1);
    }
    if let Some(node) = root.borrow().right.clone() {
        dfs(r, node, level + 1);
    }
}
```