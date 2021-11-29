# [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)

## Problem

Given the roots of two binary trees `root` and `subRoot`, return `true` if there
is a subtree of `root` with the same structure and node values of `subRoot`
and `false` otherwise.

A subtree of a binary tree `tree` is a tree that consists of a node in `tree`
and all of this node's descendants. The tree `tree` could also be considered as
a subtree of itself.

#### Constraints

* The number of nodes in the root `tree` is in the range `[1, 2000]`.
* The number of nodes in the `subRoot` tree is in the range `[1, 1000]`.
* `-10^4 <= root.val <= 10^4`
* `-10^4 <= subRoot.val <= 10^4`

#### Examples

```text
      3             4
     / \           / \
    4   5         1   2
   / \
  1   2

Input: root = [3,4,5,1,2], subRoot = [4,1,2]
Output: true
```

```text
      3             4
     / \           / \
    4   5         1   2
   / \
  1   2
     /
    0

Input: root = [3,4,5,1,2,null,null,null,null,0], subRoot = [4,1,2]
Output: false
```

#### Solution

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

pub fn is_subtree(
    root: Option<Rc<RefCell<TreeNode>>>,
    sub_root: Option<Rc<RefCell<TreeNode>>>,
) -> bool {
    if let (Some(a), Some(b)) = (&root, &sub_root) {
        return dfs(a, b);
    }

    false
}

pub fn dfs(root: &Rc<RefCell<TreeNode>>, subtree: &Rc<RefCell<TreeNode>>) -> bool {
    if match_sub_tree(root, subtree) {
        return true;
    }

    if let Some(left) = &root.borrow().left {
        if dfs(left, subtree) {
            return true;
        }
    }

    if let Some(right) = &root.borrow().right {
        if dfs(right, subtree) {
            return true;
        }
    }

    false
}

fn match_sub_tree(a: &Rc<RefCell<TreeNode>>, b: &Rc<RefCell<TreeNode>>) -> bool {
    let a = a.borrow();
    let b = b.borrow();

    if a.val != b.val {
        return false;
    }

    if a.left.is_some() != b.left.is_some() || a.right.is_some() != b.right.is_some() {
        return false;
    }

    if let (Some(a_left), Some(b_left)) = (&a.left, &b.left) {
        if !match_sub_tree(a_left, b_left) {
            return false;
        }
    }

    if let (Some(a_right), Some(b_right)) = (&a.right, &b.right) {
        if !match_sub_tree(a_right, b_right) {
            return false;
        }
    }

    true
}
```

### Taking advantage of `#(derive(Eq))`

The tree nodes have an `Eq` implementation, so instead of doing the DFS
ourselves, we can take advantage of `Eq`:

```rust

pub fn is_subtree(
    root: Option<Rc<RefCell<TreeNode>>>,
    sub_root: Option<Rc<RefCell<TreeNode>>>,
) -> bool {
    helper(&root, &sub_root)
}

fn helper(root: &Option<Rc<RefCell<TreeNode>>>, sub_root: &Option<Rc<RefCell<TreeNode>>>) -> bool {
    // Take advantage of the `Eq` derive
    if root == sub_root {
        return true;
    }

    if root.is_none() {
        return false;
    }

    return match root.as_ref() {
        None => false,
        Some(inner) => {
            helper(&inner.borrow().left, sub_root) || helper(&inner.borrow().right, sub_root)
        }
    };
}
```