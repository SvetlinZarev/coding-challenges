# [968. Binary Tree Cameras](https://leetcode.com/problems/binary-tree-cameras/)

## Problem

### Description

You are given the `root` of a binary tree. We install cameras on the tree nodes
where each camera at a node can monitor its parent, itself, and its immediate
children.

Return the minimum number of cameras needed to monitor all nodes of the tree.

### Constraints

* The number of nodes in the tree is in the range `[1, 1000]`
* `Node.val == 0`

### Examples

#### Example 1

![image](resources/968/ex1.png)

```text
Input: root = [0,0,null,0,0]
Output: 1
```

> Explanation: One camera is enough to monitor all nodes if placed as shown.

#### Example 2

![image](resources/968/ex2.png)

```text
Input: root = [0,0,null,0,null,0,null,null,0]
Output: 2
```

> Explanation: At least two cameras are needed to monitor all nodes of the tree.
> The above image shows one of the valid configurations of camera placement.

## Solutions

#### API given by LeetCode (rust)

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

### Greedy

```rust
#[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)]
enum State {
    WithCamera,
    NotCovered,
    Covered,
}

pub fn min_camera_cover(root: Option<Rc<RefCell<TreeNode>>>) -> i32 {
    let mut answer = 0;
    if let Some(root) = root.as_ref() {
        if State::NotCovered == dfs(root, &mut answer) {
            answer += 1;
        }
    }
    answer
}

fn dfs(root: &Rc<RefCell<TreeNode>>, cameras: &mut i32) -> State {
    let root_ref = root.borrow();

    // There is no point in putting cameras on leaf nodes, because it would 
    // require up to 2x the amount of cameras as compared to putting them 
    // on the leaf's parent
    if root_ref.left.is_none() && root_ref.right.is_none() {
        return State::NotCovered;
    }

    let left = match root_ref.left.as_ref() {
        // No left node, so we can treat it as if it was covered by a camera
        None => State::Covered,
        Some(node) => dfs(node, cameras),
    };

    let right = match root_ref.right.as_ref() {
        // No right node, so we can treat it as if it was covered by a camera
        None => State::Covered,
        Some(node) => dfs(node, cameras),
    };

    // If one of the child nodes is not covered, we have no choice,
    // but to put a camera at the current node
    if left == State::NotCovered || right == State::NotCovered {
        *cameras += 1;
        return State::WithCamera;
    }

    // Because of the upper check we know that both the child nodes are covered,
    // so we have to decide if this node needs cover, or is already covered by
    // one of its children
    if left == State::WithCamera || right == State::WithCamera {
        return State::Covered;
    }

    // This node is not covered, so its parent node needs to contain a camera
    State::NotCovered
}
```

### DP

TODO

