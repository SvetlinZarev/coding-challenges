# [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/)

## Problem

You are given an integer array `nums` with no duplicates. A maximum binary tree
can be built recursively from `nums` using the following algorithm:

* Create a root node whose value is the maximum value in `nums`.
* Recursively build the left subtree on the subarray prefix to the left of the
  maximum value.
* Recursively build the right subtree on the subarray suffix to the right of the
  maximum value.

Return the maximum binary tree built from `nums`.

#### Constraints

* `1 <= nums.length <= 1000`
* `0 <= nums[i] <= 1000`
* All integers in `nums` are unique.

#### Examples

![tree-diagram](resources/654/tree1.jpg)

```text
Input: nums = [3,2,1,6,0,5]
Output: [6,3,5,null,2,0,null,null,1]
Explanation: The recursive calls are as follow:
- The largest value in [3,2,1,6,0,5] is 6. Left prefix is [3,2,1] and right suffix is [0,5].
    - The largest value in [3,2,1] is 3. Left prefix is [] and right suffix is [2,1].
        - Empty array, so no child.
        - The largest value in [2,1] is 2. Left prefix is [] and right suffix is [1].
            - Empty array, so no child.
            - Only one element, so child is a node with value 1.
    - The largest value in [0,5] is 5. Left prefix is [0] and right suffix is [].
        - Only one element, so child is a node with value 0.
        - Empty array, so no child.
```

![tree-diagram](resources/654/tree2.jpg)

```text
Input: nums = [3,2,1]
Output: [3,null,2,null,1]
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


use std::cell::RefCell;
use std::rc::Rc;

impl Solution {
    pub fn construct_maximum_binary_tree(nums: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
        match nums.iter().enumerate().max_by_key(|&(idx, val)| *val) {
            None => None,
            Some((idx, &val)) => build(&nums[..idx], &nums[idx + 1..], val),
        }
    }
}

fn build(left: &[i32], right: &[i32], val: i32) -> Option<Rc<RefCell<TreeNode>>> {
    let left = match left.iter().enumerate().max_by_key(|&(idx, val)| *val) {
        None => None,
        Some((idx, &val)) => build(&left[..idx], &left[idx + 1..], val),
    };

    let right = match right.iter().enumerate().max_by_key(|&(idx, val)| *val) {
        None => None,
        Some((idx, &val)) => build(&right[..idx], &right[idx + 1..], val),
    };

    let mut node = TreeNode::new(val);
    node.left = left;
    node.right = right;

    Some(Rc::new(RefCell::new(node)))
}
```