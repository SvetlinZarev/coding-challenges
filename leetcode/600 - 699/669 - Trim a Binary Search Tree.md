# [669. Trim a Binary Search Tree](https://leetcode.com/problems/trim-a-binary-search-tree/)

## Problem

### Description

Given the `root` of a binary search tree and the lowest and highest boundaries
as `low` and `high`, trim the tree so that all its elements lies
in `[low, high]`.

Trimming the tree should not change the relative structure of the elements that
will remain in the tree (i.e., any node's descendant should remain a descendant)
. It can be proven that there is a unique answer.

Return the `root` of the trimmed binary search tree. Note that the root may
change
depending on the given bounds.

### Constraints

* The number of nodes in the tree in the range `[1, 10^4]`.
* `0 <= Node.val <= 10^4`
* The value of each node in the tree is unique.
* `root` is guaranteed to be a valid binary search tree.
* `0 <= low <= high <= 10^4`

### Examples

### Example 1

```text
   1               1
  / \       =>      \
 0   2               2

Input: root = [1,0,2], low = 1, high = 2
Output: [1,null,2]
```

#### Example 2

```text
      3                 3
     / \               /
    0   4             2
     \         =>    /
      2             1
     /
    1

Input: root = [3,0,4,null,2,null,null,1], low = 1, high = 3
Output: [3,2,null,1]
```

## Solutions

### DFS

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

pub fn trim_bst(
    root: Option<Rc<RefCell<TreeNode>>>,
    low: i32,
    high: i32,
) -> Option<Rc<RefCell<TreeNode>>> {
    let root = match root {
        None => return None,
        Some(node) => node,
    };

    let mut root_ref = root.borrow_mut();
    if root_ref.val < low {
        return trim_bst(root_ref.right.take(), low, high);
    } else if root_ref.val > high {
        return trim_bst(root_ref.left.take(), low, high);
    }

    root_ref.left = trim_bst(root_ref.left.take(), low, high);
    root_ref.right = trim_bst(root_ref.right.take(), low, high);

    drop(root_ref);
    Some(root)
}
```
