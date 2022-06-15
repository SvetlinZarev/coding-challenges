# [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

## Problem

### Description

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in
the tree.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is
defined between two nodes `p` and `q` as the lowest node in `T` that has
both `p` and `q` as descendants (where we allow a node to be a descendant of
itself).”

### Constraints

* The number of nodes in the tree is in the range `[2, 105]`.
* `-10^9 <= Node.val <= 10^9`
* All `Node.val` are unique.
* `p != q`
* `p` and `q` will exist in the tree.

### Examples

#### Example 1

![image](resources/236/ex1.png)

```text
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
```

> Explanation: The LCA of nodes 5 and 1 is 3.

#### Example 2

![image](resources/236/ex2.png)

```text
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
```

> Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant
> of itself according to the LCA definition.

#### Example 3

```text
Input: root = [1,2], p = 1, q = 2
Output: 1
```

## Solutions

### DFS

```rust
use std::cell::RefCell;
use std::rc::Rc;

// API given by LeetCode
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

pub fn lowest_common_ancestor(
    root: Option<Rc<RefCell<TreeNode>>>,
    p: Option<Rc<RefCell<TreeNode>>>,
    q: Option<Rc<RefCell<TreeNode>>>,
) -> Option<Rc<RefCell<TreeNode>>> {
    if p.is_none() || q.is_none() || root.is_none() {
        return None;
    }

    dfs(
        root.as_ref().unwrap(),
        p.as_ref().unwrap().borrow().val,
        q.as_ref().unwrap().borrow().val,
    )
}

fn dfs(root: &Rc<RefCell<TreeNode>>, p: i32, q: i32) -> Option<Rc<RefCell<TreeNode>>> {
    let root_ref = root.borrow();

    // It's safe to check this first, because a node can be ancestor of itself.
    // This it does not matter if left/right contain the other number, because 
    // either way this node will be the LCA
    if root_ref.val == p || root_ref.val == q {
        return Some(root.clone());
    }

    // Check the left sub-tree
    let mut left = None;
    if let Some(node) = root_ref.left.as_ref() {
        left = dfs(node, p, q);
    }

    // Check the right sub-tree
    let mut right = None;
    if let Some(node) = root_ref.right.as_ref() {
        right = dfs(node, p, q);
    }

    // If both sub-trees are some, than this means that the current 
    // node is the LCA
    if left.is_some() && right.is_some() {
        return Some(root.clone());
    }

    // Otherwise return:
    // * `left` if it is some (right is `None`)
    // * `right` if it is some (left is `None`)
    // * `None` if both `left` and `right` are `None`
    left.or(right)
}
```
