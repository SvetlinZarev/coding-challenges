# [662. Maximum Width of Binary Tree](https://leetcode.com/problems/maximum-width-of-binary-tree/)

## Problem

### Description

Given the root of a binary tree, return the maximum width of the given tree.

The maximum width of a tree is the maximum width among all levels.

The width of one level is defined as the length between the end-nodes (the
leftmost and rightmost non-null nodes), where the null nodes between the
end-nodes are also counted into the length calculation.

It is guaranteed that the answer will in the range of 32-bit signed integer.

### Constraints

* The number of nodes in the tree is in the range `[1, 3000]`.
* `-100 <= Node.val <= 100`

### Examples

![image](/leetcode/600%20-%20699/resources/662/ex1.jpg)

```text
Input: root = [1,3,2,5,3,null,9]
Output: 4
Explanation: The maximum width existing in the third level with the length 4 (5,3,null,9).
```

![image](/leetcode/600%20-%20699/resources/662/ex2.jpg)

```text
Input: root = [1,3,null,5,3]
Output: 2
Explanation: The maximum width existing in the third level with the length 2 (5,3).
```

![image](/leetcode/600%20-%20699/resources/662/ex2.jpg)

```text
Input: root = [1,3,2,5]
Output: 2
Explanation: The maximum width existing in the second level with the length 2 (3,2).
```

## Solutions

### BFS

```rust
use std::cell::RefCell;
use std::collections::VecDeque;
use std::rc::Rc;

// LeetCode's TreeNode definition
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

pub fn width_of_binary_tree(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let root = match root {
        Some(root) => root,
        None => return 0,
    };

    let mut queue = VecDeque::new();
    let mut next = VecDeque::new();
    queue.push_back((root, 1));

    let mut width = 0;

    while !queue.is_empty() {
        // SAFETY: we've already checked that there are elements in the queue in the `while !queue.is_empty()` loop
        let leftmost_node = unsafe { queue.front().map(|x| x.1).unwrap_unchecked() };
        let rightmost_node = unsafe { queue.back().map(|x| x.1).unwrap_unchecked() };
        width = width.max(rightmost_node - leftmost_node + 1);

        while let Some((node, width)) = queue.pop_front() {
            let node = node.borrow();
            if let Some(node) = node.left.clone() {
                next.push_back((node, width * 2));
            }
            if let Some(node) = node.right.clone() {
                next.push_back((node, width * 2 + 1));
            }
        }

        std::mem::swap(&mut queue, &mut next);
    }

    width
}
```