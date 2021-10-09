# [129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/)

## Problem

You are given the root of a binary tree containing digits from 0 to 9 only.

Each root-to-leaf path in the tree represents a number.

```text
For example, the root-to-leaf path 1 -> 2 -> 3 represents the number 123.
```

Return the total sum of all root-to-leaf numbers. Test cases are generated so
that the answer will fit in a 32-bit integer.

A leaf node is a node with no children.

#### Example

```text
Input: root = [1,2,3]
Output: 25
Explanation:
The root-to-leaf path 1->2 represents the number 12.
The root-to-leaf path 1->3 represents the number 13.
Therefore, sum = 12 + 13 = 25.
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

pub fn sum_numbers(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut result = 0;
    preorder(root, 0, &mut result);
    result
}


fn preorder(root: Option<Rc<RefCell<TreeNode>>>, sum: i32, total: &mut i32) {
    if let Some(node) = root {
        let sum = sum * 10 + node.borrow().val;
        let prev = *total;

        preorder(node.borrow().left.clone(), sum, total);
        preorder(node.borrow().right.clone(), sum, total);

        if prev == *total {
            *total += sum;
        }
    }
}
```