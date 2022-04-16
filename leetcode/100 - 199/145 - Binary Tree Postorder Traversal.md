# [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

## Problem

### Description

Given the root of a binary tree, return the postorder traversal of its nodes'
values.

### Constraints

* The number of nodes in the tree is in the range `[0, 100]`.
* `-100 <= Node.val <= 100`

### Examples

```text
Input: root = [1,null,2,3]
Output: [3,2,1]
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
use std::rc::Rc;
use std::cell::RefCell;


pub fn postorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut nodes = vec![];
    postorder(root, &mut nodes);
    nodes
}


fn postorder(root: Option<Rc<RefCell<TreeNode>>>, nodes: &mut Vec<i32>) {
    if let Some(node) = root {
        postorder(node.borrow().left.clone(), nodes);
        postorder(node.borrow().right.clone(), nodes);
        nodes.push(node.borrow().val);
    }
}
```

## Related Problems

* [94. Binary Tree Inorder Traversal](/leetcode/000%20-%20099/94%20-%20Binary%20Tree%20Inorder%20Traversal.md)
* [144. Binary Tree Preorder Traversal](144%20-%20Binary%20Tree%20Preorder%20Traversal.md)
