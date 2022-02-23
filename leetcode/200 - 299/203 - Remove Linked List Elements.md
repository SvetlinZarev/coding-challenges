# [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

## Problem

Given the `head` of a linked list and an integer `val`, remove all
the nodes of the linked list that has `Node.val == val`, and return
the new `head`.


#### Constraints

* The number of nodes in the list is in the range `[0, 10^4]`
* `1 <= Node.val <= 50`
* `0 <= val <= 50`


#### Examples

```text
Input: head = [1,2,6,3,4,5,6], val = 6
Output: [1,2,3,4,5]
```

```text
Input: head = [], val = 1
Output: []
```

```text
Input: head = [7,7,7,7], val = 7
Output: []
```

## Solution

```rust
// Definition for singly-linked list.
// #[derive(PartialEq, Eq, Clone, Debug)]
// pub struct ListNode {
//   pub val: i32,
//   pub next: Option<Box<ListNode>>
// }
// 
// impl ListNode {
//   #[inline]
//   fn new(val: i32) -> Self {
//     ListNode {
//       next: None,
//       val
//     }
//   }
// }


pub fn remove_elements(mut head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
    let mut dummy = None;
    let mut tail = &mut dummy;

    while let Some(mut node) = head.take() {
        head = node.next.take();

        if node.val != val {
            *tail = Some(node);
            tail = &mut tail.as_mut().unwrap().next;
        }
    }

    dummy
}
```

Or without `.unwrap()`:

```rust
pub fn remove_elements(head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
    let mut head = head;
    let mut ptr = &mut head;

    loop {
        match ptr {
            None => break,
            Some(node) if node.val == val => *ptr = node.next.take(),
            Some(node) => ptr = &mut node.next,
        }
    }

    head
}
```
