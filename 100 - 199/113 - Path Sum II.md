# [113. Path Sum II](https://leetcode.com/problems/path-sum-ii/)

## Problem

Given the root of a binary tree and an integer `targetSum`, return all **
root-to-leaf** paths where the sum of the node values in the path
equals `targetSum`. Each path should be returned as a list of the node values,
not node references.

A root-to-leaf path is a path starting from the root and ending at any leaf
node. A leaf is a node with no children.

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


pub fn path_sum(root: Option<Rc<RefCell<TreeNode>>>, target_sum: i32) -> Vec<Vec<i32>> {
    let mut result = vec![];
    dfs(&mut result, root, &mut vec![], target_sum, 0);
    result
}

fn dfs(
    result: &mut Vec<Vec<i32>>,
    root: Option<Rc<RefCell<TreeNode>>>,
    path: &mut Vec<i32>,
    target: i32,
    current: i32,
) {
    if let Some(node) = root {
        let v = node.borrow().val;
        let c = current + v;
        path.push(v);

        let node = node.borrow();
        if node.left.is_some() || node.right.is_some() {
            dfs(result, node.left.clone(), path, target, c);
            dfs(result, node.right.clone(), path, target, c);
        } else if c == target {
            result.push(path.clone());
        }

        path.pop();
    }
}
```