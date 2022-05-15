# [1302. Deepest Leaves Sum](https://leetcode.com/problems/deepest-leaves-sum/)

## Problem

### Description

Given the `root` of a binary tree, return the sum of values of its deepest
leaves.

### Constraints

* The number of nodes in the tree is in the range `[1, 10^4]`.
* `1 <= Node.val <= 100`

### Examples

#### Example 1

![image](resources/1302/ex1.png)

```text
Input: root = [1,2,3,4,5,null,6,7,null,null,null,null,8]
Output: 15
```

#### Example 2

```text
Input: root = [6,7,8,2,7,1,3,9,null,1,4,null,null,null,5]
Output: 19
```

## Solutions

#### Common code from the problem definition

```rust
use std::cell::RefCell;
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

### BFS

By using BFS we can find the last level of the tree (i.e. the level that does
not have any children nodes), and then sum the values of those nodes.

This solution will use additional memory proportional to the number of nodes in
the last two levels.

```rust
pub fn deepest_leaves_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut queue = vec![];
    let mut next = vec![];

    if let Some(root) = root {
        queue.push(root);
    }

    while !queue.is_empty() {
        for node in queue.iter() {
            let node_ref = node.borrow();
            if let Some(left) = node_ref.left.clone() {
                next.push(left);
            }
            if let Some(right) = node_ref.right.clone() {
                next.push(right);
            }
        }

        if next.is_empty() {
            break;
        }

        queue.clear();
        std::mem::swap(&mut queue, &mut next);
    }

    queue.into_iter().map(|x| x.borrow().val).sum()
}
```

### DFS #1

We can solve the problem using DFS. This will allow us to use memory
proportional to the max_depth of the tree.

We will traverse the tree once in order to find the maximum depth of the tree.
Then we will traverse it a second time in order to sum the values of all nodes
which are placed at the maximum depth.

```rust

pub fn deepest_leaves_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    if let Some(root) = root {
        let target_depth = max_depth(&root, 0);
        return dfs_sum(&root, 0, target_depth);
    }

    0
}

fn max_depth(root: &Rc<RefCell<TreeNode>>, depth: u32) -> u32 {
    let node_ref = root.borrow();

    let mut left_depth = depth;
    if let Some(node) = &node_ref.left {
        left_depth = max_depth(node, depth + 1);
    }

    let mut right_depth = depth;
    if let Some(node) = &node_ref.right {
        right_depth = max_depth(node, depth + 1);
    }

    left_depth.max(right_depth)
}

fn dfs_sum(root: &Rc<RefCell<TreeNode>>, depth: u32, target: u32) -> i32 {
    let node_ref = root.borrow();
    if depth == target {
        return node_ref.val;
    }

    let mut sum = 0;
    if let Some(node) = &node_ref.left {
        sum += dfs_sum(node, depth + 1, target);
    }
    if let Some(node) = &node_ref.right {
        sum += dfs_sum(node, depth + 1, target);
    }

    sum
}
```

### DFS #2

Instead of traversing the tree twice, we can traverse it only once by following
the algorithm:

1. If the current depth is less than the currently known maximum depth, then
   recursively continue with the child nodes if any
2. If the current depth is equal to the currently known maximum depth, then add
   the current node value to the sum, Continue with the child nodes if any
3. If the current depth is greater than the currently known maximum depth, then
   update the value for the maximum depth and replace the sum with the current
   node value. Continue with the child nodes if any

```rust

pub fn deepest_leaves_sum(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut sum = 0;

    if let Some(root) = root {
        let mut max_depth = 0;
        dfs_sum(&root, 0, &mut max_depth, &mut sum);
    }

    sum
}

fn dfs_sum(root: &Rc<RefCell<TreeNode>>, depth: u32, max_depth: &mut u32, sum: &mut i32) {
    let node_ref = root.borrow();
    if depth == *max_depth {
        *sum += node_ref.val;
    } else if depth > *max_depth {
        *max_depth = depth;
        *sum = node_ref.val;
    }

    if let Some(node) = &node_ref.left {
        dfs_sum(node, depth + 1, max_depth, sum);
    }
    if let Some(node) = &node_ref.right {
        dfs_sum(node, depth + 1, max_depth, sum);
    }
}
```
