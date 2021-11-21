# [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

## Problem

Given two integer arrays `inorder` and `postorder` where `inorder` is the
inorder traversal of a binary tree and `postorder` is the postorder traversal of
the same tree, construct and return the binary tree.

#### Constraints

* `1 <= inorder.length <= 3000`
* `postorder.length == inorder.length`
* `-3000 <= inorder[i], postorder[i] <= 3000`
* `inorder` and `postorder` consist of unique values.
* Each value of `postorder` also appears in `inorder`.
* `inorder` is guaranteed to be the inorder traversal of the tree.
* `postorder` is guaranteed to be the postorder traversal of the tree.

#### Examples

```text
Input: 
> inorder = [9,3,15,20,7]
> postorder = [9,15,7,20,3]

Output: 
> [3,9,20,null,null,15,7]

      3
     / \
    9   20
       /  \
      15   7
```

```text
Input: inorder = [-1], postorder = [-1]
Output: [-1]
```

## Solution

#### Observations

* The last element of the postorder array contains the tree root
* All elements in the postorder array whose index in the inorder array is less
  than the index of the root element in the inorder array are the left sub-tree.
  The elements whose index in the inorder array is greater than the root index
  are the right sub-tree
* We must iterate the postorder array in reverse order and build first the right
  sub-tree, then the left sub-tree

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

pub fn build_tree(inorder: Vec<i32>, postorder: Vec<i32>) -> Option<Rc<RefCell<TreeNode>>> {
    assert_eq!(postorder.len(), inorder.len());
    if postorder.len() == 0 {
        return None;
    }

    let indexes = inorder
        .iter()
        .copied()
        .enumerate()
        .map(|(idx, val)| (val, idx))
        .collect::<HashMap<_, _>>();

    // use 1-based indexing. An index of 0 means that we have processed all elements
    let mut root_index = postorder.len();
    subtree(&postorder, &mut root_index, 0, postorder.len(), &indexes)
}

fn subtree(
    postorder: &[i32],
    index: &mut usize,
    left: usize,
    right: usize,
    indexes: &HashMap<i32, usize>,
) -> Option<Rc<RefCell<TreeNode>>> {
    if *index == 0 {
        return None;
    }
    *index -= 1;

    let root = postorder[*index];
    let mut node = TreeNode::new(root);

    let &inorder_root_index = indexes.get(&root).unwrap();

    if *index > 0 {
        let value = postorder[*index - 1];
        let inorder_index = indexes[&value];
        if inorder_index > inorder_root_index && inorder_index < right {
            node.right = subtree(postorder, index, inorder_root_index, right, indexes);
        }
    }

    if *index > 0 {
        let value = postorder[*index - 1];
        let inorder_index = indexes[&value];
        if inorder_index < inorder_root_index && inorder_index >= left {
            node.left = subtree(postorder, index, left, inorder_root_index, indexes);
        }
    }

    Some(Rc::new(RefCell::new(node)))
}
```

## Related Problems

* [105. Construct Binary Tree from Preorder and Inorder Traversal](105%20-%20Construct%20Binary%20Tree%20from%20Preorder%20and%20Inorder%20Traversal.md)
