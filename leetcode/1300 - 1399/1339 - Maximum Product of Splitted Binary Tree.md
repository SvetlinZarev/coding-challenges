# [1339. Maximum Product of Splitted Binary Tree](https://leetcode.com/problems/maximum-product-of-splitted-binary-tree/)

## Problem

Given the root of a binary tree, split the binary tree into two subtrees by
removing one edge such that the product of the sums of the subtrees is
maximized.

Return the maximum product of the sums of the two subtrees. Since the answer may
be too large, return it modulo `109 + 7`.

Note that you need to maximize the answer before taking the mod and not after
taking it.

**Hint:** If we know the sum of a subtree, the answer
is `max( (total_sum - subtree_sum) * subtree_sum)` in each node.

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
use std::collections::HashMap;
use std::rc::Rc;

type NodePtr = Rc<RefCell<TreeNode>>;

impl Solution {
    pub fn max_product(root: Option<NodePtr>) -> i32 {
        match root {
            None => 0,
            Some(node) => {
                let mut sub_trees = Vec::with_capacity(1024 * 32);
                let total = dfs(node, &mut sub_trees) as i64;

                let mut max = 0;
                for v in sub_trees {
                    let (mul, _) = (total - v).overflowing_mul(v);
                    max = max.max(mul);
                }

                (max % (10i64.pow(9) + 7)) as i32
            }
        }
    }
}

fn dfs(root: NodePtr, sub_trees: &mut Vec<i64>) -> i32 {
    let r = root.borrow();
    let mut sum = r.val;

    if let Some(node) = &r.left {
        sum += dfs(node.clone(), sub_trees);
    }

    if let Some(node) = &r.right {
        sum += dfs(node.clone(), sub_trees);
    }

    sub_trees.push(sum as i64);

    sum
}
```