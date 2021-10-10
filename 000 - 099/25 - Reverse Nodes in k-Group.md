# [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)

## Problem

Given a linked list, reverse the nodes of a linked list k at a time and return
its modified list.

k is a positive integer and is less than or equal to the length of the linked
list. If the number of nodes is not a multiple of k then left-out nodes, in the
end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be
changed.

#### Examples

```text
Input: head = [1,2,3,4,5], k = 2
Output: [2,1,4,3,5]
```

```text
Input: head = [1,2,3,4,5], k = 3
Output: [3,2,1,4,5]
```

```text
Input: head = [1,2,3,4,5], k = 1
Output: [1,2,3,4,5]
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

pub fn reverse_k_group(head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
    let mut stack = Vec::with_capacity(k as usize);
    let mut src = head;
    let mut head = ListNode::new(0);
    let mut tail = &mut head.next;

    while let Some(mut node) = src.take() {
        src = node.next.take();

        stack.push(node);
        if stack.len() == k as usize {
            while let Some(node) = stack.pop() {
                *tail = Some(node);
                tail = &mut tail.as_mut().unwrap().next;
            }
        }
    }

    for node in stack {
        *tail = Some(node);
        tail = &mut tail.as_mut().unwrap().next;
    }

    head.next.take()
}
```

Note: it's possible to do the reversing in-place