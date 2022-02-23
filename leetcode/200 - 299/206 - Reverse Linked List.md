# [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

## Problem

Given the `head` of a singly linked list, reverse the list, and return the
reversed list.

#### Constraints

* The number of nodes in the list is the range `[0, 5000]`.

#### Examples

```text
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

```text
Input: head = [1,2]
Output: [2,1]
```

```text
Input: head = []
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

pub fn reverse_list(list: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut old = list;
    let mut head = ListNode::new(0);

    while let Some(mut node) = old.take() {
        old = node.next.take();
        node.next = head.next.take();
        head.next = Some(node);
    }

    head.next.take()
}

```