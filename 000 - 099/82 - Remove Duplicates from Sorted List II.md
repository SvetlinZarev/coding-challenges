# [82. Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

## Problem

Given the head of a sorted linked list, delete all nodes that have duplicate
numbers, leaving only distinct numbers from the original list. Return the linked
list sorted as well.

#### Constraints

* The number of nodes in the list is in the range `[0, 300]`.
* `-100 <= Node.val <= 100`
* The list is guaranteed to be sorted in ascending order.

#### Examples

```text
Input: head = [1,2,3,3,4,4,5]
Output: [1,2,5]
```

```text
Input: head = [1,1,1,2,3]
Output: [2,3]
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

    let mut prev = match head.take() {
        None => return None,
        Some(mut node) => {
            head = node.next.take();
            node
        }
    };

    let mut skip = false;
    while let Some(mut node) = head.take() {
        head = node.next.take();

        if node.val == prev.val {
            skip = true;
            continue;
        }

        if skip {
            skip = false;
            prev = node;
        } else {
            std::mem::swap(&mut prev, &mut node);
            *tail = Some(node);
            tail = &mut tail.as_mut().unwrap().next;
        }
    }

    if !skip {
        *tail = Some(prev);
    }

    dummy.next.take()
}
```