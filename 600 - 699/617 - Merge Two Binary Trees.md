# [617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/)

## Problem

You are given two binary trees `root1` and `root2`.

Imagine that when you put one of them to cover the other, some nodes of the two
trees are overlapped while the others are not. You need to merge the two trees
into a new binary tree. The merge rule is that if two nodes overlap, then sum
node values up as the new value of the merged node. Otherwise, the NOT `null`
node will be used as the node of the new tree.

Return the merged tree.

**Note:** The merging process must start from the root nodes of both trees.

#### Example

```text
Input: root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
Output: [3,4,5,5,4,null,7]
```

## Solution

```rust
// Definition for a binary tree node.
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


pub fn merge_trees(
    root1: Option<Rc<RefCell<TreeNode>>>,
    root2: Option<Rc<RefCell<TreeNode>>>,
) -> Option<Rc<RefCell<TreeNode>>> {
    match (root1, root2) {
        (None, None) => None,
        (Some(tree), None) => Some(tree),
        (None, Some(tree)) => Some(tree),
        (Some(a), Some(b)) => Some(dfs_merge(a, b)),
    }
}


fn dfs_merge(a: Rc<RefCell<TreeNode>>, b: Rc<RefCell<TreeNode>>) -> Rc<RefCell<TreeNode>> {
    let mut first_tree = a.borrow_mut();
    let second_tree = b.borrow();

    first_tree.val += second_tree.val;

    match (first_tree.left.clone(), second_tree.left.clone()) {
        (Some(al), Some(bl)) => {
            dfs_merge(al, bl);
        }
        (None, Some(bl)) => {
            first_tree.left.replace(bl);
        }
        _ => {}
    }

    match (first_tree.right.clone(), second_tree.right.clone()) {
        (Some(ar), Some(br)) => {
            dfs_merge(ar, br);
        }
        (None, Some(br)) => {
            first_tree.right.replace(br);
        }
        _ => {}
    }

    a.clone()
}
```