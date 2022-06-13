# [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)

## Problem

### Description

Given a root node reference of a BST and a key, delete the node with the given
key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

1. Search for a node to remove.
2. If the node is found, delete the node.

### Constraints

* `-10^5 <= key <= 10^5`
* `-10^5 <= Node.val <= 10^5`
* The number of nodes in the tree is in the range `[0, 10^4]`.
* Each node has a unique value.
* `root` is a valid binary search tree.

### Examples

#### Example 1

![image](resources/450/ex1.jpg)

```text
Input: root = [5,3,6,2,4,null,7], key = 3
Output: [5,4,6,2,null,null,7]

Explanation: Given key to delete is 3. So we find the node with value 3 and delete it.
One valid answer is [5,4,6,2,null,null,7], shown in the above BST.
Please notice that another valid answer is [5,2,6,null,4,null,7] and it's also accepted.
```

#### Example 2

![image](resources/450/ex2.jpg)

```text
Input: root = [5,3,6,2,4,null,7], key = 0
Output: [5,3,6,2,4,null,7]
Explanation: The tree does not contain a node with value = 0.
```

#### Example 3

```text
Input: root = [], key = 0
Output: []
```

## Solutions

#### API given by LeetCode (rust)

```rust
use std::rc::Rc;
use std::cell::RefCell;

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

```

### Naive solution

1. Using inorder traversal collect all nodes' values in a vector
2. Find the target key in the vector using a binary search and remove it
3. Recursively rebuild the BST as
   in [108. Convert Sorted Array to Binary Search Tree](/leetcode/100%20-%20199/108%20-%20Convert%20Sorted%20Array%20to%20Binary%20Search%20Tree.md)

### Recursive solution

```rust
use std::cmp::Ordering;

pub fn delete_node(root: Option<Rc<RefCell<TreeNode>>>, key: i32) -> Option<Rc<RefCell<TreeNode>>> {
    if let Some(root) = root {
        let mut node = root.borrow_mut();
        let value = node.val;

        match key.cmp(&value) {
            Ordering::Less => {
                node.left = delete_node(node.left.take(), key);
            }

            Ordering::Greater => {
                node.right = delete_node(node.right.take(), key);
            }
            Ordering::Equal => {
                //It's a leaf node, without any children
                if node.left.is_none() && node.right.is_none() {
                    return None;
                }

                // Only one child
                if node.left.is_none() || node.right.is_none() {
                    return if node.left.is_none() {
                        node.right.take()
                    } else {
                        node.left.take()
                    };
                }

                // We have two children:
                //
                // The node has 2 children - In this case, in order to conserve the 
                // BST properties, we need to replace the node with it's inorder 
                // successor (The next node that comes in the inorder traversal) i.e; 
                // we need to replace it with either (it doesn't matter which one):
                //    1. The greatest value node in it's left subtree (or)
                //    2. The smallest value node in it's right subtree
                //    and return the root.
                //
                // In this example we will find the largest element on the left sub-tree
                let mut temp = node.left.clone().unwrap();
                loop {
                    let borrowed = temp.borrow();
                    let right = match &borrowed.right {
                        Some(node) => node.clone(),
                        None => break,
                    };

                    drop(borrowed);
                    temp = right;
                }

                // replace the current node's value with the largest value from the left sub-tree
                node.val = temp.borrow().val;

                // Now we have a duplicate value, so we have to remove the largest value 
                // from the left sub-tree, which means that we should recursively call
                // delete_node() on the left subtree
                node.left = delete_node(node.left.take(), node.val);
            }
        }

        drop(node);
        return Some(root);
    }

    None
}
```

## Resources

* [BST Node deletion algorithm @Wikipedia](https://en.wikipedia.org/wiki/Binary_search_tree#Deletion)

## Related Problems

* [94. Binary Tree Inorder Traversal](000%20-%20099/94%20-%20Binary%20Tree%20Inorder%20Traversal.md)
* [108. Convert Sorted Array to Binary Search Tree](/leetcode/100%20-%20199/108%20-%20Convert%20Sorted%20Array%20to%20Binary%20Search%20Tree.md)