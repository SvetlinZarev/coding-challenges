# [1008. Construct Binary Search Tree from Preorder Traversal](https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/)

## Problem

Given an array of integers `preorder`, which represents the preorder traversal
of a BST (i.e., binary search tree), construct the tree and return its root.

It is guaranteed that there is always possible to find a binary search tree with
the given requirements for the given test cases.

A binary search tree is a binary tree where for every node, any descendant
of `Node.left` has a value strictly less than `Node.val`, and any descendant
of `Node.right` has a value strictly greater than `Node.val`.

A preorder traversal of a binary tree displays the value of the node first, then
traverses `Node.left`, then traverses `Node.right`.

#### Constraints

* All the values of preorder are unique.
* `1 <= preorder[i] <= 10^8`
* `1 <= preorder.length <= 100`

#### Examples

```text
Input: preorder = [8,5,1,7,10,12]
Output: [8,5,10,1,7,null,12]
```

```text
Input: preorder = [1,3]
Output: [1,null,3]
```

```text
Input: [7,20,19,12]
Output: [7,null,20,19,null,12]
```

## Solutions

#### API given by LeetCode

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

### O(n log n) average, O(n^2) worst case

```rust

pub fn bst_from_preorder(nodes: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
    assert!(!nodes.is_empty());

    let root_value = nodes[0];
    let root = Rc::new(RefCell::new(TreeNode::new(root_value)));

    subtree(root.clone(), &nodes[1..]);

    Some(root)
}

fn subtree(root: Rc<RefCell<TreeNode>>, nodes: &[i32]) {
    if nodes.is_empty() {
        return;
    }

    let mut root = root.borrow_mut();
    let idx = nodes
        .iter()
        .copied()
        .enumerate()
        .find(|(_, val)| *val > root.val)
        .map(|(idx, _)| idx)
        .unwrap_or(nodes.len());

    let left_nodes = &nodes[..idx];
    let right_nodes = &nodes[idx..];

    if !left_nodes.is_empty() {
        let node = Rc::new(RefCell::new(TreeNode::new(left_nodes[0])));
        root.left = Some(node.clone());
        subtree(node, &left_nodes[1..]);
    }

    if !right_nodes.is_empty() {
        let node = Rc::new(RefCell::new(TreeNode::new(right_nodes[0])));
        root.right = Some(node.clone());
        subtree(node, &right_nodes[1..]);
    }
}
```

### O(n) solution

Instead of searching for the splitting point, we pass the parent value to the
function. When the current value is larger than the parent, then we stop
building that subtree

```rust
pub fn bst_from_preorder(nodes: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
    build_tree(&nodes, i32::MAX, &mut 0)
}

fn build_tree(nodes: &[i32], parent: i32, idx: &mut usize) -> Option<Rc<RefCell<TreeNode>>> {
    if *idx >= nodes.len() || nodes[*idx] > parent {
        return None;
    }

    let mut root = TreeNode::new(nodes[*idx]);
    *idx += 1;

    root.left = build_tree(&nodes, nodes[*idx - 1], idx);
    root.right = build_tree(&nodes, parent, idx);

    Some(Rc::new(RefCell::new(root)))
}
```