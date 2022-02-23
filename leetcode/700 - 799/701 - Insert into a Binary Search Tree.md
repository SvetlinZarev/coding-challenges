# [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/)

## Problem

You are given the root node of a binary search tree (BST) and a value to insert
into the tree. Return the root node of the BST after the insertion. It is
guaranteed that the new value does not exist in the original BST.

Notice that there may exist multiple valid ways for the insertion, as long as
the tree remains a BST after insertion. You can return any of them.

#### Constraints

* The number of nodes in the tree will be in the range `[0, 10^4]`.
* `-10^8 <= Node.val <= 10^8`
* All the values `Node.val` are unique.
* `-10^8 <= val <= 10^8`
* It's guaranteed that val does not exist in the original BST.

#### Examples

```text
Input: root = [4,2,7,1,3], val = 5
Output: [4,2,7,1,3,5]
```

```text
Input: root = [40,20,60,10,30,50,70], val = 25
Output: [40,20,60,10,30,50,70,null,null,25]
```

```text
Input: root = [4,2,7,1,3,null,null,null,null,null,null], val = 5
Output: [4,2,7,1,3,5]
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

pub fn insert_into_bst(
    root: Option<Rc<RefCell<TreeNode>>>,
    val: i32,
) -> Option<Rc<RefCell<TreeNode>>> {
    let new_node = Some(Rc::new(RefCell::new(TreeNode::new(val))));
    if root.is_none() {
        return new_node;
    }

    let mut current = root.clone();
    let mut prev = root.clone();

    while let Some(node) = current.take() {
        {
            let n = node.borrow();
            if val < n.val {
                current = n.left.clone();
            } else {
                current = n.right.clone();
            }
        }

        prev = Some(node);
    }

    if let Some(node) = prev {
        if node.borrow().val > val {
            node.borrow_mut().left = new_node;
        } else {
            node.borrow_mut().right = new_node;
        }
    }

    root
}
```