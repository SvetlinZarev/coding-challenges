# [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

## Problem

### Description

Given the `root` of a binary search tree, and an integer `k`, return the `K`th
smallest value (1-indexed) of all the values of the nodes in the tree.

### Constraints

* The number of nodes in the tree is `n`.
* `1 <= k <= n <= 10^4`
* `0 <= Node.val <= 10^4`

### Examples

```text
       3
      / \
  -> 1   4
      \
       2
Input: root = [3,1,4,null,2], k = 1
Output: 1
```

```text
                     5
                    / \
                -> 3   6
                  / \
                 2   4
                /
               1

Input: root = [5,3,6,2,4,null,null,1], k = 3
Output: 3
```

## Solution

### API given by leetcode

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
```

### Recursive inorder traversal

```rust
pub fn kth_smallest(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
    if let Some(root) = root.as_ref() {
        return inorder(root, &mut k.clone()).unwrap_or(0);
    }

    0
}

fn inorder(root: &Rc<RefCell<TreeNode>>, n: &mut i32) -> Option<i32> {
    let root = root.borrow();

    // inorder -> first visit the left sub-tree
    if let Some(node) = root.left.as_ref() {
        if let Some(val) = inorder(node, n) {
            return Some(val);
        }
    }

    // inorder -> visit the current node and decrement the node count
    *n -= 1;
    if *n == 0 {
        return Some(root.val);
    }

    // inorder -> then visit the right sub-tree
    if let Some(node) = root.right.as_ref() {
        if let Some(val) = inorder(node, n) {
            return Some(val);
        }
    }

    None
}
```

### Iterative inorder traversal

```rust

pub fn kth_smallest(root: Option<Rc<RefCell<TreeNode>>>, k: i32) -> i32 {
    let mut stack = vec![];

    let mut count = 0;
    let mut node = root.clone();

    while !stack.is_empty() || node.is_some() {
        while let Some(n) = node.take() {
            node = n.borrow().left.clone();
            stack.push(n);
        }

        let nth_node = stack.pop().unwrap();
        let nth_ref = nth_node.borrow();
        node = nth_ref.right.clone();

        count += 1;
        if count == k {
            return nth_ref.val;
        }
    }

    // There are less than K nodes in the tree
    -1
}
```

## Related Problems

* [173. Binary Search Tree Iterator](/leetcode/100%20-%20199/173%20-%20Binary%20Search%20Tree%20Iterator.md)
