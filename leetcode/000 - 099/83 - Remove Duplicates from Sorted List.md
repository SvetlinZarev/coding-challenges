# [83. Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)

## Problem

Given the head of a sorted linked list, delete all duplicates such that each
element appears only once. Return the linked list sorted as well.

#### Examples

```text
Input: head = [1,1,2]
Output: [1,2]
```

```text
Input: head = [1,1,2,3,3]
Output: [1,2,3]
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

pub fn delete_duplicates(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut dummy = ListNode::new(0);
    let mut tail = &mut dummy.next;

    let mut prev = None;

    while let Some(mut node) = head.take() {
        head = node.next.take();
        if Some(node.val) == prev {
            continue;
        }

        prev = Some(node.val);
        *tail = Some(node);
        tail = &mut tail.as_mut().unwrap().next;
    }

    dummy.next.take()
}
```