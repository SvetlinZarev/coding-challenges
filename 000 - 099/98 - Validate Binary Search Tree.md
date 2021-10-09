# [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)

## Problem

Given the root of a binary tree, determine if it is a valid binary search tree
(BST).

A valid BST is defined as follows:

* The left subtree of a node contains only nodes with keys less than the node's
  key.
* The right subtree of a node contains only nodes with keys greater than the
  node's key.
* Both the left and right subtrees must also be binary search trees.

#### Constraints

* The number of nodes in the tree is in the range `[1, 10^4]`.
* `-2^31 <= Node.val <= 2^31 - 1`

#### Example

```text
Input: root = [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```

## Solution

In inorder traversal of a valid BST will yield the node's values in a sorted
order. Therefore, we can do an inorder traversal of the tree and check if the
current node's value is larger than the previous:

```rust
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


pub fn is_valid_bst(root: Option<Rc<RefCell<TreeNode>>>) -> bool {
    match root {
        None => false,
        Some(root) => {
            // We don't really need to store the whole array to check 
            // if it's sorted. We only need the previous element, which
            // is initially `None`
            let mut last = None;
            inorder(root, &mut last)
        }
    }
}

fn inorder(root: Rc<RefCell<TreeNode>>, last_value: &mut Option<i32>) -> bool {
    let root = root.borrow();

    if let Some(left) = root.left.clone() {
        // If the left sub-tree is not valid BST, then the whole tree is not
        if !inorder(left, last_value) {
            return false;
        }
    }

    // Note the `.replace()` operation. We take the previous value and 
    // set the new "prev" in one step
    if let Some(last) = last_value.replace(root.val) {
        // If there was a previous node, verify that it was 
        // smaller than the current value. Otherwise it's not
        // a valid BST
        if last >= root.val {
            return false;
        }
    }

    if let Some(right) = root.right.clone() {
        // If the right sub-tree is not valid BST, then the whole tree is not
        if !inorder(right, last_value) {
            return false;
        }
    }

    true
}
```