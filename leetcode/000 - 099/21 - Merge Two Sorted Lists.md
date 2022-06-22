# [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

## Problem

### Description

Merge two sorted linked lists and return it as a sorted list. The list should be
made by splicing together the nodes of the first two lists.

Both lists are sorted in non-decreasing order.

### Constraints

* The number of nodes in both lists is in the range `[0, 50]`.
* `-100 <= Node.val <= 100`
* Both list1 and `list2` are sorted in non-decreasing order.

### Examples

```text
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

```text
Input: list1 = [], list2 = []
Output: []
```

```text
Input: list1 = [], list2 = [0]
Output: [0]
```

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