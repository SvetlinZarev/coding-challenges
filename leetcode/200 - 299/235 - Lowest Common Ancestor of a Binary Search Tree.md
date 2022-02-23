# [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## Problem

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two
given nodes in the BST.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is
defined between two nodes `p` and `q` as the lowest node in T that has both `p`
and `q` as descendants (where we allow a node to be a descendant of itself).”

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


pub fn lowest_common_ancestor(
    r: Option<Rc<RefCell<TreeNode>>>,
    p: Option<Rc<RefCell<TreeNode>>>,
    q: Option<Rc<RefCell<TreeNode>>>,
) -> Option<Rc<RefCell<TreeNode>>> {
    let (lo, hi) = pq(p, q);

    let mut current = r;
    while let Some(r) = current.take() {
        let root = r.borrow();
        if lo <= root.val && hi >= root.val {
            drop(root);
            return Some(r);
        }

        if lo < root.val {
            current = root.left.clone();
        } else {
            current = root.right.clone();
        }
    }

    None
}

fn pq(p: Option<Rc<RefCell<TreeNode>>>, q: Option<Rc<RefCell<TreeNode>>>) -> (i32, i32) {
    let pv = p.as_ref().unwrap().borrow().val;
    let qv = q.as_ref().unwrap().borrow().val;

    (pv.min(qv), pv.max(qv))
}
```