# [103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

## Problem

### Description

Given the `root` of a binary tree, return the zigzag level order traversal of
its nodes' values. (i.e., from left to right, then right to left for the next
level and alternate between).

### Constraints

* The number of nodes in the tree is in the range `[0, 2000]`.
* `-100 <= Node.val <= 100`

### Examples

![image](resources/103/ex1.jpg)

```text
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[20,9],[15,7]]
```

```text
Input: root = [1]
Output: [[1]]
```

```text
Input: root = []
Output: []
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
pub fn zigzag_level_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<Vec<i32>> {
    let mut answer = vec![];

    if let Some(root) = root {
        left_to_right(&mut answer, &root, 0);
        right_to_left(&mut answer, &root, 0);
    }

    answer
}

fn left_to_right(answer: &mut Vec<Vec<i32>>, root: &Rc<RefCell<TreeNode>>, depth: usize) {
    if answer.len() <= depth {
        answer.push(vec![]);
    }

    let root_ref = root.borrow();
    if depth % 2 == 0 {
        answer[depth].push(root_ref.val);
    }

    if let Some(node) = &root_ref.left {
        left_to_right(answer, node, depth + 1);
    }

    if let Some(node) = &root_ref.right {
        left_to_right(answer, node, depth + 1);
    }
}

fn right_to_left(answer: &mut Vec<Vec<i32>>, root: &Rc<RefCell<TreeNode>>, depth: usize) {
    if answer.len() <= depth {
        answer.push(vec![]);
    }

    let root_ref = root.borrow();
    if depth % 2 != 0 {
        answer[depth].push(root_ref.val);
    }

    if let Some(node) = &root_ref.right {
        right_to_left(answer, node, depth + 1);
    }

    if let Some(node) = &root_ref.left {
        right_to_left(answer, node, depth + 1);
    }
}
```

### BFS

TODO