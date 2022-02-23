# [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

## Problem

Merge two sorted linked lists and return it as a sorted list. The list should be
made by splicing together the nodes of the first two lists.

Both lists are sorted in non-decreasing order.

## Solution

```rust
pub fn merge_two_lists(
    l1: Option<Box<ListNode>>,
    l2: Option<Box<ListNode>>,
) -> Option<Box<ListNode>> {
    let mut l1 = l1;
    let mut l2 = l2;
    let mut head = ListNode::new(0);
    let mut tail = &mut head.next;

    while l1.is_some() && l2.is_some() {
        let next = if l1.as_ref().unwrap().val <= l2.as_ref().unwrap().val {
            let mut temp = l1.unwrap();
            l1 = temp.next.take();
            temp
        } else {
            let mut temp = l2.unwrap();
            l2 = temp.next.take();
            temp
        };

        tail.replace(next);
        tail = &mut tail.as_mut().unwrap().next;
    }

    if let Some(next) = l1 {
        tail.replace(next);
    } else if let Some(next) = l2 {
        tail.replace(next);
    }

    head.next.take()
}
```