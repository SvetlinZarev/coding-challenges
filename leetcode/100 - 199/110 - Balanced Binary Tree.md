# [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

## Problem

### Description

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

* a binary tree in which the left and right subtrees of every node differ in
  height by no more than 1.

### Constraints

* The number of nodes in the tree is in the range `[0, 5000]`.
* `-10^4 <= Node.val <= 10^4`

### Examples

#### Example 1

![image](resources/110/ex1.jpg)

```text
Input: root = [3,9,20,null,null,15,7]
Output: true
```

#### Example 2

![image](resources/110/ex2.jpg)

```text
Input: root = [1,2,2,3,3,null,null,4,4]
Output: false
```

#### Example 3

```text
Input: root = []
Output: true
```

## Solutions

#### Node definition from leetcode:

```rust
// Definition for a binary tree node.
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

### DFS, O(n)

```rust
use std::cell::RefCell;
use std::rc::Rc;

pub fn is_balanced(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
    match root {
        None => true,
        Some(root) => dfs_balanced_height(root) >= 0,
    }
}

fn dfs_balanced_height(node: Rc<RefCell<TreeNode>>) -> i32 {
    let node = node.borrow();

    let left = match node.left.clone() {
        None => 0,
        Some(child) => dfs_balanced_height(child),
    };

    let right = match node.right.clone() {
        None => 0,
        Some(child) => dfs_balanced_height(child),
    };

    if left == -1 || right == -1 || (left - right).abs() > 1 {
        return -1;
    }

    left.max(right) + 1
}
```

## Related Problems

* [104. Maximum Depth of Binary Tree](104%20-%20Maximum%20Depth%20of%20Binary%20Tree.md)