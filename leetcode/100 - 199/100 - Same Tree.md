# [100. Same Tree](https://leetcode.com/problems/same-tree/)

## Problem

### Description

Given the roots of two binary trees `p` and `q`, write a function to check if
they are the same or not.

Two binary trees are considered the same if they are structurally identical, and
the nodes have the same value.

### Constraints

* The number of nodes in both trees is in the range `[0, 100]`.
* `-10^4 <= Node.val <= 10^4`

### Examples

```text
      1       1
     / \     / \
    2   3   2   3

Input: p = [1,2,3], q = [1,2,3]
Output: true
```

```text
      1       1
     /         \
    2           2

Input: p = [1,2], q = [1,null,2]
Output: false
```

```text
      1       1
     / \     / \
    2   1   1   2

Input: p = [1,2,1], q = [1,1,2]
Output: false
```

## Solutions

#### API given by LeetCode

```rust
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug, PartialEq, Eq)]
pub struct TreeNode {
    pub val: i32,
    pub left: Option<Rc<RefCell<TreeNode>>>,
    pub right: Option<Rc<RefCell<TreeNode>>>,
}

impl TreeNode {
    #[inline]
    pub fn new(val: i32) -> Self {
        TreeNode {
            val,
            left: None,
            right: None,
        }
    }
}
```

### DFS

```rust
pub fn is_same_tree(p: Option<Rc<RefCell<TreeNode>>>, q: Option<Rc<RefCell<TreeNode>>>) -> bool {
    match (p, q) {
        (None, None) => true,
        (Some(x), Some(y)) => {
            let xref = x.borrow();
            let yref = y.borrow();

            if xref.val != yref.val {
                return false;
            }

            if !is_same_tree(xref.left.clone(), yref.left.clone()) {
                return false;
            }

            if !is_same_tree(xref.right.clone(), yref.right.clone()) {
                return false;
            }

            true
        }
        _ => false,
    }
}
```
