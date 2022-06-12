# [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

## Problem

### Description

Given the root of a binary tree, check whether it is a mirror of itself (i.e.,
symmetric around its center).

### Constraints

* The number of nodes in the tree is in the range `[1, 1000]`.
* `-100 <= Node.val <= 100`

### Examples

![image](resources/101/ex1.jpg)

```text
Input: root = [1,2,2,3,4,4,3]
Output: true
```

![image](resources/101/ex2.jpg)

```text
Input: root = [1,2,2,null,3,null,3]
Output: false
```

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


pub fn is_symmetric(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
    if let Some(root) = root {
        let node = root.borrow();
        return match (node.left.clone(), node.right.clone()) {
            (Some(l), Some(r)) => dfs(l, r),
            (None, None) => true,
            _ => false,
        };
    }

    true
}

fn dfs(left: Rc<RefCell<TreeNode>>, right: Rc<RefCell<TreeNode>>) -> bool {
    let l = left.borrow();
    let r = right.borrow();

    if l.val != r.val {
        return false;
    }

    let is_symmetric = match (l.left.clone(), r.right.clone()) {
        (Some(l), Some(r)) => dfs(l, r),
        (None, None) => true,
        _ => false,
    };

    if !is_symmetric {
        return false;
    }

    match (l.right.clone(), r.left.clone()) {
        (Some(l), Some(r)) => dfs(l, r),
        (None, None) => true,
        _ => false,
    }
}
```