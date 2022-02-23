# [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

## Problem

Given two integer arrays preorder and inorder where preorder is the preorder
traversal of a binary tree and inorder is the inorder traversal of the same
tree, construct and return the binary tree.

#### Constraints

* `1 <= preorder.length <= 3000`
* `inorder.length == preorder.length`
* `-3000 <= preorder[i], inorder[i] <= 3000`
* `preorder` and `inorder` consist of unique values.
* Each value of `inorder` also appears in `preorder`.
* `preorder` is guaranteed to be the preorder traversal of the tree.
* `inorder` is guaranteed to be the inorder traversal of the tree.

#### Examples

```text
Input: 
> preorder = [3,9,20,15,7], 
> inorder = [9,3,15,20,7]

Output: 
> [3,9,20,null,null,15,7]

      3
     / \
    9   20
       /  \
      15   7
```

```text
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

## Solution

#### Observations

* The first element of the preorder array contains the tree root
* All elements in the preorder array whose index in the inorder array is less
  than the index of the root element in the inorder array are the left sub-tree.
  The elements whose index in the inorder array is greater than the root index
  are the right sub-tree

**Example:**

```text
> preorder = [3,9,20,15,7], 
> inorder = [9,3,15,20,7]
```

* The root is `3` and its inorder index is `1`.
* `9` has an inorder index of `0` which is less than `1`, so `9` is in the left
  sub-tree
* `20` has an inorder index of `3`, which is greater than `1`, thus it's in the
  right sub-tree

#### Source

```rust
use std::rc::Rc;
use std::cell::RefCell;
use std::collections::HashMap;

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
            right: None
        }
    }
}


pub fn build_tree(preorder: Vec<i32>, inorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
    assert_eq!(preorder.len(), inorder.len());
    if preorder.len() == 0 {
        return None;
    }

    let indexes = inorder
        .iter()
        .copied()
        .enumerate()
        .map(|(idx, val)| (val, idx))
        .collect::<HashMap<_, _>>();

    let mut root_index = 0;
    subtree(&preorder, &mut root_index, 0, preorder.len(), &indexes)
}

fn subtree(
    preorder: &[i32],
    index: &mut usize,
    left: usize,
    right: usize,
    indexes: &HashMap<i32, usize>,
) -> Option<Rc<RefCell<TreeNode>>> {
    let root = preorder[*index];
    let &inorder_root_index = indexes.get(&root).unwrap();

    *index += 1;
    let mut node = TreeNode::new(root);

    if *index < preorder.len() {
        let value = preorder[*index];
        let inorder_index = indexes[&value];
        if inorder_index < inorder_root_index && inorder_index >= left {
            node.left = subtree(preorder, index, left, inorder_root_index, indexes);
        }
    }

    if *index < preorder.len() {
        let value = preorder[*index];
        let inorder_index = indexes[&value];
        if inorder_index > inorder_root_index && inorder_index < right {
            node.right = subtree(preorder, index, inorder_root_index + 1, right, indexes);
        }
    }

    Some(Rc::new(RefCell::new(node)))
}
```

## Related Problems

* [106. Construct Binary Tree from Inorder and Postorder Traversal](106%20-%20Construct%20Binary%20Tree%20from%20Inorder%20and%20Postorder%20Traversal.md)