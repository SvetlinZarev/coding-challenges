# [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)

## Problem

Given the head of a singly linked list, return the middle node of the linked
list.

If there are two middle nodes, return the second middle node.

#### Examples

```text
Input: head = [1,2,*3*,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

```text
Input: head = [1,2,3,*4*,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

## Solutions

### Fast/Slow pointer

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

pub fn middle_node(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut fast = head.as_ref();
    let mut slow = head.as_ref();

    while let Some(node) = fast {
        match node.next.as_ref() {
            None => break,
            Some(fast_next) => {
                fast = fast_next.next.as_ref();
                slow = slow.unwrap().next.as_ref();
            }
        }
    }

    // unfortunately we cannot have 2 mut pointers or mutable and immutable 
    // pointer at the sametime, so we have to clone the whole second half 
    // of the list
    slow.map(|n| n.clone())
}
```

### Two-pass solution

Iterate over the list once to find out the number of nodes. Then iterate the
list for a second time and cut it in half. This would avoid the problem of
cloning the list.