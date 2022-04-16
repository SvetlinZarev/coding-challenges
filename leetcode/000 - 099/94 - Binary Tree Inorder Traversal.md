# [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

## Problem

### Description

Given the root of a binary tree, return the inorder traversal of its nodes'
values.

### Constraints

* The number of nodes in the tree is in the range `[0, 100]`.
* `-100 <= Node.val <= 100`

### Examples

```text
Input: root = [1,null,2,3]
Output: [1,3,2]
```

```text
Input: root = []
Output: []
```

```text
Input: root = [1]
Output: [1]
```

## Solution

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

use std::cell::RefCell;
use std::rc::Rc;

pub fn inorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut result = vec![];
    inorder(&mut result, root);
    result
}


fn inorder(path: &mut Vec<i32>, root: Option<Rc<RefCell<TreeNode>>>) {
    if let Some(node) = root {
        inorder(path, node.borrow().left.clone());
        path.push(node.borrow().val);
        inorder(path, node.borrow().right.clone());
    }
}
```

## Related Problems

* [144. Binary Tree Preorder Traversal](/leetcode/100%20-%20199/144%20-%20Binary%20Tree%20Preorder%20Traversal.md)
* [145. Binary Tree Postorder Traversal](/leetcode/100%20-%20199/145%20-%20Binary%20Tree%20Postorder%20Traversal.md)
