# [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

## Problem

### Description

Given an integer array `nums` where the elements are sorted in ascending order,
convert it to a height-balanced binary search tree.

A height-balanced binary tree is a binary tree in which the depth of the two
subtrees of every node never differs by more than one.

### Constraints

* `1 <= nums.length <= 10^4`
* `-10^4 <= nums[i] <= 10^4`
* `nums` is sorted in a strictly increasing order.

### Examples

### Example 1

![image](resources/108/ex1a.jpg)
![image](resources/108/ex1b.jpg)

```text
Input: nums = [-10,-3,0,5,9]
Output: [0,-3,9,-10,null,5]
Explanation: [0,-10,5,null,-3,null,9] is also accepted:
```

### Example 2

![image](resources/108/ex2.jpg)

```text
Input: nums = [1,3]
Output: [3,1]
Explanation: [1,null,3] and [3,1] are both height-balanced BSTs.
```

## Solutions

#### API given by leetcode

```rust
use std::cell::{Ref, RefCell};
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
```

### Recursion

```rust
pub fn sorted_array_to_bst(nums: &[i32]) -> Option<Rc<RefCell<TreeNode>>> {
    if nums.is_empty() {
        return None;
    }

    let mid = nums.len() / 2;
    Some(Rc::new(RefCell::new(TreeNode {
        val: nums[mid],
        left: sorted_array_to_bst(&nums[..mid]),
        right: sorted_array_to_bst(&nums[mid + 1..]),
    })))
}
```