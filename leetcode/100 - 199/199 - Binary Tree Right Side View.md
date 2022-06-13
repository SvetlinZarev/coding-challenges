# [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)

## Problem

### Description

Given the `root` of a binary tree, imagine yourself standing on the right side
of it, return the values of the nodes you can see ordered from top to bottom.

### Constraints

* The number of nodes in the tree is in the range `[0, 100]`.
* `-100 <= Node.val <= 100`

### Examples

```text
        1 <-
       / \
      2   3 <-
       \   \
        5   4 <-

Input: root = [1,2,3,null,5,null,4]
Output: [1,3,4]
```

```text
Input: root = [1,null,3]
Output: [1,3]
```

```text
Input: root = []
Output: []
```

## Solutions

### DFS

```rust
use std::cell::RefCell;
use std::rc::Rc;

// TreeNode as given by LeetCode
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

pub fn right_side_view(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut answer = vec![];

    if let Some(root) = &root {
        dfs(&mut answer, root, 0);
    }

    answer
}

fn dfs(answer: &mut Vec<i32>, root: &Rc<RefCell<TreeNode>>, depth: usize) {
    let root_ref = root.borrow();
    if answer.len() <= depth {
        answer.push(root_ref.val);
    }

    if let Some(right) = &root_ref.right {
        dfs(answer, right, depth + 1);
    }

    if let Some(left) = &root_ref.left {
        dfs(answer, left, depth + 1);
    }
}
```