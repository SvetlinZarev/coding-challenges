# [109. Convert Sorted List to Binary Search Tree](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree)

## Problem

### Description

Given the `head` of a singly linked list where elements are sorted in ascending
order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in
which the depth of the two subtrees of every node never differ by more than 1.

### Constraints

* The number of nodes in head is in the range `[0, 2 * 10^4]`.
* `-10^5 <= Node.val <= 10^5`

### Examples

#### Example 1

![image](resources/109/ex1.jpg)

```text
Input: head = [-10,-3,0,5,9]
Output: [0,-3,9,-10,null,5]
```

> Explanation: One possible answer is `[0,-3,9,-10,null,5]`, which represents
> the shown height balanced BST.

#### Example 2

```text
Input: head = []
Output: []
```

## Solutions

#### API Given By LeetCode (rust)

```rust
use std::cell::RefCell;
use std::rc::Rc;

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

### Collect the list to a vector/array

We can collect the list to a vector/array and apply the solution
from [108. Convert Sorted Array to Binary Search Tree](108%20-%20Convert%20Sorted%20Array%20to%20Binary%20Search%20Tree.md)

### Recursive pre-order

Although this approach works great for arrays, we can do better with a
linked-list. THe issue with this approach is that we cannot find the middle of
the list in `O(1)` time, thus we have to always skip half of the list
in `split_list()`. THus the time complexity should be `O(n log n)`

```rust
// Calculate the list length only once at the beginning. If we pass 
// it as a parameter,then we can avoid iteration over the list again 
// and again for re-calculating it for the sub-trees
pub fn sorted_list_to_bst(head: Option<Box<ListNode>>) -> Option<Rc<RefCell<TreeNode>>> {
    let length = list_length(head.as_ref());
    sorted_list_with_len_to_bst(head, length)
}

fn sorted_list_with_len_to_bst(
    head: Option<Box<ListNode>>,
    length: usize,
) -> Option<Rc<RefCell<TreeNode>>> {
    if length == 0 {
        return None;
    }

    let (left, mid, right) = split_list(head, length);
    Some(Rc::new(RefCell::new(TreeNode {
        val: mid.val,
        left: sorted_list_with_len_to_bst(left, length / 2),
        right: sorted_list_with_len_to_bst(right, (length - 1) / 2),
    })))
}

fn list_length(head: Option<&Box<ListNode>>) -> usize {
    let mut node = head;
    let mut length = 0;

    while let Some(n) = node {
        node = n.next.as_ref();
        length += 1;
    }

    length
}

fn split_list(
    list: Option<Box<ListNode>>,
    length: usize,
) -> (Option<Box<ListNode>>, Box<ListNode>, Option<Box<ListNode>>) {
    // This function can be called only if there is at least one element in the list
    assert!(length > 0);

    let mut head = list;

    let mut left = None;
    let mut tail = &mut left;

    for _ in 0..length / 2 {
        let mut node = head.take().unwrap();
        head = node.next.take();

        tail = &mut tail.insert(node).next;
    }

    let mut mid = head.take().unwrap();
    let right = mid.next.take();

    (left, mid, right)
}
```

### Recursive in-order

With this approach we traverse the linked list only once, thus the time
complexity is `O(n)`

```rust

pub fn sorted_list_to_bst(mut head: Option<Box<ListNode>>) -> Option<Rc<RefCell<TreeNode>>> {
    let length = list_length(head.as_ref());
    list_to_bst(&mut head, 0, length)
}

pub fn list_to_bst(
    head: &mut Option<Box<ListNode>>,
    start: usize,
    end: usize,
) -> Option<Rc<RefCell<TreeNode>>> {
    if start >= end {
        return None;
    }

    let mid = start + (end - start) / 2;

    // build the left sub-tree
    let left = list_to_bst(head, start, mid);

    // The current head will give us the value og the current node.
    // Take the current head and replace it with the next node, in
    // order to be able to build the right sub-tree later

    let mut current = head.take().unwrap();
    *head = current.next.take();

    // build the left sub-tree
    let right = list_to_bst(head, mid + 1, end);

    Some(Rc::new(RefCell::new(TreeNode {
        val: current.val,
        left,
        right,
    })))
}

fn list_length(head: Option<&Box<ListNode>>) -> usize {
    let mut node = head;
    let mut length = 0;

    while let Some(n) = node {
        node = n.next.as_ref();
        length += 1;
    }

    length
}
```

## Related Problems

* [108. Convert Sorted Array to Binary Search Tree](108%20-%20Convert%20Sorted%20Array%20to%20Binary%20Search%20Tree.md)
