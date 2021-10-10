# [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)

## Problem

Given a linked list, swap every two adjacent nodes and return its head. You must
solve the problem without modifying the values in the list's nodes (i.e., only
nodes themselves may be changed.)

#### Examples

```text
Input: head = [1,2,3,4]
Output: [2,1,4,3]
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


pub fn swap_pairs(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut list = ListNode::new(0);
    let mut tail = &mut list.next;

    let mut temp = None;
    while let Some(mut node) = head.take() {
        head = node.next.take();

        match temp.take() {
            None => temp = Some(node),
            Some(temp) => {
                node.next = Some(temp);
                *tail = Some(node);
                tail = &mut tail
                    .as_mut().unwrap().next // i.e. node's next
                    .as_mut().unwrap().next; // i.e. temp
            }
        }
    }

    *tail = temp; // in case there is a leftover
    list.next.take()
}
```