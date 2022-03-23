# [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

## Problem

### Description

Given the root of a binary tree, return its maximum depth.

A binary tree's maximum depth is the number of nodes along the longest path from
the root node down to the farthest leaf node.

### Constraints

* The number of nodes in the tree is in the range `[0, 10^4]`.
* `-100 <= Node.val <= 100`

### Examples

```text
Input: root = [3,9,20,null,null,15,7]
Output: 3
```

```text
Input: root = [1,null,2]
Output: 2
```

## Solution

### DFS

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

pub fn max_depth(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    dfs(root, 0)
}


fn dfs(root: Option<Rc<RefCell<TreeNode>>>, depth: i32) -> i32 {
    match root {
        None => depth,
        Some(node) => {
            let left = dfs(node.borrow().left.clone(), depth + 1);
            let right = dfs(node.borrow().right.clone(), depth + 1);
            left.max(right)
        }
    }
}
```

## Related Problems

* [110. Balanced Binary Tree](110%20-.%20Balanced%20Binary%20Tree.md)