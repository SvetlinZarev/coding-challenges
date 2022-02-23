# [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)

## Problem

Given the `root` of a binary tree, return all root-to-leaf paths in any order.

A leaf is a node with no children.

#### Constraints

* `-100 <= Node.val <= 100`
* The number of nodes in the tree is in the range `[1, 100]`.

#### Examples

```text
     1
    / \
   2   3
    \
     5
     
Input: root = [1,2,3,null,5]
Output: ["1->2->5","1->3"]
```

```text
Input: root = [1]
Output: ["1"]
```

## Solution
```rust
// FROM THE LEETCODE API: 

// #[derive(Debug, PartialEq, Eq)]
// pub struct TreeNode {
//     pub val: i32,
//     pub left: Option<Rc<RefCell<TreeNode>>>,
//     pub right: Option<Rc<RefCell<TreeNode>>>,
// }
// 
// impl TreeNode {
//     #[inline]
//     pub fn new(val: i32) -> Self {
//         TreeNode {
//             val,
//             left: None,
//             right: None,
//         }
//     }
// }
// 
// use std::cell::RefCell;
// use std::rc::Rc;

pub fn binary_tree_paths(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<String> {
    let mut sln = vec![];
    if let Some(root) = root {
        preorder(root, &mut sln, &mut vec![]);
    }
    sln
}

fn preorder(root: Rc<RefCell<TreeNode>>, sln: &mut Vec<String>, path: &mut Vec<String>) {
    let root = root.borrow();
    path.push(format!("{}", root.val));

    let mut is_leaf = true;

    if let Some(left) = root.left.clone() {
        preorder(left, sln, path);
        is_leaf = false;
    }

    if let Some(right) = root.right.clone() {
        preorder(right, sln, path);
        is_leaf = false;
    }

    if is_leaf {
        sln.push(path.join("->"));
    }

    path.pop();
}
```