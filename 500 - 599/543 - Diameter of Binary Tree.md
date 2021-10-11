# [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)

## Problem

Given the root of a binary tree, return the length of the diameter of the tree.

The diameter of a binary tree is the length of the longest path between any two
nodes in a tree. This path may or may not pass through the root.

The length of a path between two nodes is represented by the number of edges
between them.

#### Constraints

* The number of nodes in the tree is in the range `[1, 10^4]`.

#### Examples

```text
Input: root = [1,2]
Output: 1
```

```text
Input: root = [1,2,3,4,5]
Output: 3
Explanation: 3 is the length of the path [4,2,1,3] or [5,2,1,3].
```

## Solution

The tree node definition as given by LeetCode:

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
            right: None
        }
    }
}
```

### DFS

#### A less efficient, but easier to understand implementation

* Time: 4 ms

```rust
use std::rc::Rc;
use std::cell::RefCell;

pub fn diameter_of_binary_tree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut max = 0;
    if let Some(root) = root {
        dfs(root, &mut max);
    }
    max
}


fn dfs(root: Rc<RefCell<TreeNode>>, max: &mut i32) -> (i32, i32) {
    let root = root.borrow();
    let mut left = 0;
    let mut right = 0;

    if let Some(node) = root.left.clone() {
        let (l, r) = dfs(node, max);
        left = l.max(r) + 1;
    }

    if let Some(node) = root.right.clone() {
        let (l, r) = dfs(node, max);
        right = l.max(r) + 1;
    }

    *max = (*max).max(left + right);

    (left, right)
}
```

#### Or a bit more efficient implementation:

* Time: 0 ms

```rust
 pub fn diameter_of_binary_tree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    match root {
        None => 0,
        Some(root) => dfs(root).1,
    }
}

fn dfs(root: Rc<RefCell<TreeNode>>) -> (i32, i32) {
    let root = root.borrow();
    let mut diameter = 0;

    let mut left = 0;
    if let Some(node) = root.left.clone() {
        let (longest_path, max_diameter) = dfs(node);
        left = longest_path;
        diameter = max_diameter;
    }

    let mut right = 0;
    if let Some(node) = root.right.clone() {
        let (longest_path, max_diameter) = dfs(node);
        right = longest_path;
        diameter = diameter.max(max_diameter);
    }

    (left.max(right) + 1, diameter.max(left + right))
}
```