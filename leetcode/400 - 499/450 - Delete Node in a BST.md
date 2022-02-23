# [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)

## Problem

Given a root node reference of a BST and a key, delete the node with the given
key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

1. Search for a node to remove.
2. If the node is found, delete the node.

#### Constraints

* `-10^5 <= key <= 10^5`
* `-10^5 <= Node.val <= 10^5`
* The number of nodes in the tree is in the range `[0, 10^4]`.
* Each node has a unique value.
* `root` is a valid binary search tree.

#### Examples

```text
Input: root = [5,3,6,2,4,null,7], key = 3
Output: [5,4,6,2,null,null,7]

Explanation: Given key to delete is 3. So we find the node with value 3 and delete it.
One valid answer is [5,4,6,2,null,null,7], shown in the above BST.
Please notice that another valid answer is [5,2,6,null,4,null,7] and it's also accepted.
```

```text
Input: root = [5,3,6,2,4,null,7], key = 0
Output: [5,3,6,2,4,null,7]
Explanation: The tree does not contain a node with value = 0.
```

```text
Input: root = [], key = 0
Output: []
```

## Solution

```rust

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
                // we need to replace it with either :
                //    1. The greatest value node in it's left subtree (or)
                //    2. The smallest value node in it's right subtree
                //    and return the root.
                let mut temp = node.left.clone().unwrap();
                loop {
                    let borrowed = temp.borrow();
                    let right = match &borrowed.right {
                        None => break,
                        Some(node) => node.clone(),
                    };

                    drop(borrowed);
                    temp = right;
                }

                node.val = temp.borrow().val;
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
