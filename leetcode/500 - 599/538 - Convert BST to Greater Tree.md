# [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/)

## Problem

### Description

Given the `root` of a Binary Search Tree (BST), convert it to a **Greater Tree**
such that every key of the original BST is changed to the original key plus the
sum of all keys greater than the original key in BST.

As a reminder, a binary search tree is a tree that satisfies these constraints:

* The left subtree of a node contains only nodes with keys **less than** the
  node's key.
* The right subtree of a node contains only nodes with keys **greater than** the
  node's key.
* Both the left and right subtrees must also be binary search trees.

### Constraints

* The number of nodes in the tree is in the range `[0, 10^4]`.
* `-10^4 <= Node.val <= 10^4`
* All the values in the tree are unique.
* `root` is guaranteed to be a valid binary search tree.

### Examples

#### Example 1

![image](resources/538/ex1.png)

```text
Input: root = [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
Output: [30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

### Example 2

```text
Input: root = [0,null,1]
Output: [1,null,1]
```

## Solutions

### DFS + Prefix Sum

From the diagram of `example 1` above, we can see that if we perform a
reversed (from right to left) `inorder` traversal of the tree we would get the
prefix sum of the three nodes.

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

pub fn convert_bst(root: Option<Rc<RefCell<TreeNode>>>) -> Option<Rc<RefCell<TreeNode>>> {
    if let Some(root) = root.clone() {
        inorder(root, 0);
    }

    root
}

fn inorder(root: Rc<RefCell<TreeNode>>, prefix_sum: i32) -> i32 {
    let mut prefix_sum = prefix_sum;
    let mut root = root.borrow_mut();

    if let Some(node) = root.right.clone() {
        prefix_sum = inorder(node, prefix_sum);
    }

    prefix_sum += root.val;
    root.val = prefix_sum;

    if let Some(node) = root.left.clone() {
        prefix_sum = inorder(node, prefix_sum);
    }

    prefix_sum
}
```

## Related Problems

* [94. Binary Tree Inorder Traversal](/leetcode/000%20-%20099/94%20-%20Binary%20Tree%20Inorder%20Traversal.md)
