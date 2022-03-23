# [1367. Linked List in Binary Tree](https://leetcode.com/problems/linked-list-in-binary-tree/)

## Problem

### Description

Given a binary tree `root` and a linked list with `head` as the first node.

Return `True` if all the elements in the linked list starting from the head
correspond to some downward path connected in the binary tree otherwise
return `False`.

In this context downward path means a path that starts at some node and goes
downwards.

### Constraints

* The number of nodes in the tree will be in the range `[1, 2500]`.
* The number of nodes in the list will be in the range `[1, 100]`.
* `1 <= Node.val <= 100` for each node in the linked list and binary tree.

### Examples

#### Example 1

![image](resources/1367/ex1.png)

```text
Input: head = [4,2,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: true
Explanation: Nodes in blue form a subpath in the binary Tree.  
```

#### Example 2

![image](resources/1367/ex2.png)

```text
Input: head = [1,4,2,6], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: true
```

#### Example 3

```text
Input: head = [1,4,2,6,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
Output: false
Explanation: There is no path in the binary tree that contains all the elements of the linked list from head.
```

## Solutions

#### Tree & List nodes as defined by LeetCode

```rust
#[derive(PartialEq, Eq, Clone, Debug)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>,
}

impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode { next: None, val }
    }
}

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

### DFS

```rust

use std::cell::RefCell;
use std::rc::Rc;

pub fn is_sub_path(head: Option<Box<ListNode>>, root: Option<Rc<RefCell<TreeNode>>>) -> bool {
    // The problem statement guarantees us that we have at least 1 list & 1 tree nodes
    let list_node = head.unwrap();
    let tree_node = root.unwrap();

    check(&list_node, &list_node, tree_node)
}

fn check(list_head: &ListNode, list_node: &ListNode, tree_node: Rc<RefCell<TreeNode>>) -> bool {
    let (value, left, right) = {
        let node = tree_node.borrow();
        (node.val, node.left.clone(), node.right.clone())
    };

    if list_node.val == value {
        let next_list_node = match list_node.next.as_ref() {
            None => return true,
            Some(next) => next,
        };

        if let Some(child) = left.clone() {
            if check(list_head, next_list_node, child) {
                return true;
            }
        }

        if let Some(child) = right.clone() {
            if check(list_head, next_list_node, child) {
                return true;
            }
        }
    }

    //TODO: Is it safe to compare pointers ?
    // Is it faster than `if list_node != list_head` ?

    // It's important to start from scratch only from the left/right 
    // children of the node from which we started, otherwise we'll be
    // checking the same thing too many times and the tests will time-out.
    if std::ptr::eq(list_head, list_node) {
        if let Some(child) = left {
            if check(list_head, list_head, child) {
                return true;
            }
        }

        if let Some(child) = right {
            if check(list_head, list_head, child) {
                return true;
            }
        }
    }

    false
}
```