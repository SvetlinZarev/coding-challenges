# [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

## Problem

### Description

Given the root of a binary tree, return the preorder traversal of its nodes'
values.

### Constraints

* The number of nodes in the tree is in the range `[0, 100]`.
* `-100 <= Node.val <= 100`

### Examples

```text
Input: root = [1,null,2,3]
Output: [1,2,3]
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


pub fn preorder_traversal(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut nodes = vec![];
    preorder(root, &mut nodes);
    nodes
}

fn preorder(root: Option<Rc<RefCell<TreeNode>>>, nodes: &mut Vec<i32>) {
    if let Some(node) = root {
        nodes.push(node.borrow().val);

        preorder(node.borrow().left.clone(), nodes);
        preorder(node.borrow().right.clone(), nodes);
    }
}
```

## Related Problems

* [94. Binary Tree Inorder Traversal](/leetcode/000%20-%20099/94%20-%20Binary%20Tree%20Inorder%20Traversal.md)
* [145. Binary Tree Postorder Traversal](/leetcode/100%20-%20199/145%20-%20Binary%20Tree%20Postorder%20Traversal.md)
