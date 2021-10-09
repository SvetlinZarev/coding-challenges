# [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/)

## Problem

You are given the root of a binary search tree (BST) and an integer `val`.

Find the node in the BST that the node's value equals `val` and return the
subtree rooted with that node. If such a node does not exist, return `null`.

#### Constraints

* The number of nodes in the tree is in the range `[1, 5000]`.
* `1 <= val <= 10^7`
* `1 <= Node.val <= 10^7`
* `root` is a binary search tree.

## Solution

```rust
use std::rc::Rc;
use std::cell::RefCell;

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

pub fn search_bst(root: Option<Rc<RefCell<TreeNode>>>, val: i32) -> Option<Rc<RefCell<TreeNode>>> {
    let mut node = root;

    while let Some(n) = node.take() {
        let x = n.borrow();
        if val == x.val {
            return Some(n.clone());
        }

        if val < x.val {
            node = x.left.clone();
        } else {
            node = x.right.clone();
        }
    }

    None
}
```