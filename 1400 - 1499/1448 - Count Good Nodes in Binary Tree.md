# [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)

## Problem

Given a binary tree root, a node X in the tree is named good if in the path from
root to X there are no nodes with a value greater than X.

Return the number of good nodes in the binary tree.

#### Examples

```text
Input: root = [3,1,4,3,null,1,5]
Output: 4
Explanation: Nodes in blue are good.
Root Node (3) is always a good node.
Node 4 -> (3,4) is the maximum value in the path starting from the root.
Node 5 -> (3,4,5) is the maximum value in the path
Node 3 -> (3,1,3) is the maximum value in the path.
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

use std::cell::RefCell;
use std::rc::Rc;

pub fn good_nodes(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    match root {
        None => 0,
        Some(node) => dfs(node, i32::MIN),
    }
}


fn dfs(node: Rc<RefCell<TreeNode>>, max: i32) -> i32 {
    let node = node.borrow();
    let mut count = 0;
    let mut max = max;

    if max <= node.val {
        max = node.val;
        count += 1;
    }

    if let Some(node) = &node.left {
        count += dfs(node.clone(), max);
    }

    if let Some(node) = &node.right {
        count += dfs(node.clone(), max);
    }

    count
}
```