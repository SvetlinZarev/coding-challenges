# [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## Problem

### Description

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two
given nodes in the BST.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is
defined between two nodes `p` and `q` as the lowest node in T that has both `p`
and `q` as descendants (where we allow a node to be a descendant of itself).”

### Constraints

* `The number of nodes in the tree is in the range [2, 105].`
* `-10^9 <= Node.val <= 10^9`
* All `Node.val` are unique.
* `p != q`
* `p` and `q` will exist in the BST.

### Examples

#### Example 1

![image](resources/235/ex1.png)

```text
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```

#### Example 2

![image](resources/235/ex2.png)

```text
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
Output: 2
Explanation: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
```

#### Example 3

```text
Input: root = [2,1], p = 2, q = 1
Output: 2
```

## Solutions

### Take advantage of the BST properties

We can take advantage of the BST properties - the left children are smaller than
the parent node, and the right children are larger

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


pub fn lowest_common_ancestor(
    r: Option<Rc<RefCell<TreeNode>>>,
    p: Option<Rc<RefCell<TreeNode>>>,
    q: Option<Rc<RefCell<TreeNode>>>,
) -> Option<Rc<RefCell<TreeNode>>> {
    let (lo, hi) = pq(p, q);

    let mut current = r;
    while let Some(r) = current.take() {
        let root = r.borrow();
        if lo <= root.val && hi >= root.val {
            drop(root);
            return Some(r);
        }

        if lo < root.val {
            current = root.left.clone();
        } else {
            current = root.right.clone();
        }
    }

    None
}

fn pq(p: Option<Rc<RefCell<TreeNode>>>, q: Option<Rc<RefCell<TreeNode>>>) -> (i32, i32) {
    let pv = p.as_ref().unwrap().borrow().val;
    let qv = q.as_ref().unwrap().borrow().val;

    (pv.min(qv), pv.max(qv))
}
```

## Related Problems

* [236. Lowest Common Ancestor of a Binary Tree](236%20-%20Lowest%20Common%20Ancestor%20of%20a%20Binary%20Tree.md)
