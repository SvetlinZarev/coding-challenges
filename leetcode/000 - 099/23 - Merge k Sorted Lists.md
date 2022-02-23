# [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)

## Problem

You are given an array of `k` linked-lists lists, each linked-list is sorted in
ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

#### Example

```text
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

## Solution

### Using a priority queue

* Time Complexity: O(N*log K)  where `K` is the number of linked lists.

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


use std::cmp::{Ordering, Reverse};
use std::collections::BinaryHeap;

pub fn merge_k_lists(mut lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
    let mut merged = ListNode::new(0);
    let mut tail = &mut merged.next;

    let mut pq = BinaryHeap::with_capacity(lists.len());
    for list in lists {
        if let Some(node) = list {
            pq.push(Reverse(NodeCmp(node)));
        }
    }

    while let Some(Reverse(mut node)) = pq.pop() {
        if let Some(next) = node.0.next.take() {
            pq.push(Reverse(NodeCmp(next)));
        }

        tail.replace(node.0);
        tail = &mut tail.as_mut().unwrap().next;
    }
    merged.next.take()
}


struct NodeCmp(Box<ListNode>);

impl PartialOrd<NodeCmp> for NodeCmp {
    fn partial_cmp(&self, other: &NodeCmp) -> Option<Ordering> {
        self.0.val.partial_cmp(&other.0.val)
    }
}

impl Ord for NodeCmp {
    fn cmp(&self, other: &Self) -> Ordering {
        self.0.val.cmp(&other.0.val)
    }
}

impl PartialEq<NodeCmp> for NodeCmp {
    fn eq(&self, other: &NodeCmp) -> bool {
        self.0.val.eq(&other.0.val)
    }
}

impl Eq for NodeCmp {
    //
}
```

### Merge the lists one by one

* Time complexity : O(N*K) where `K` is the number of linked lists and `N` is
  the number of nodes in the final list

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


pub fn merge_k_lists(mut lists: Vec<Option<Box<ListNode>>>) -> Option<Box<ListNode>> {
    let mut merged = ListNode::new(0);

    while let Some(list) = lists.pop() {
        let mut a = merged.next.take();
        let mut b = list;
        let mut tail = &mut merged.next;

        while a.is_some() && b.is_some() {
            let next = if a.as_ref().unwrap().val <= b.as_ref().unwrap().val {
                let mut temp = a.unwrap();
                a = temp.next.take();
                temp
            } else {
                let mut temp = b.unwrap();
                b = temp.next.take();
                temp
            };

            tail.replace(next);
            tail = &mut tail.as_mut().unwrap().next;
        }

        if let Some(next) = a {
            tail.replace(next);
        } else if let Some(next) = b {
            tail.replace(next);
        }
    }

    merged.next.take()
}

```

## Related problems

* [264. Ugly Number II](/leetcode/200%20-%20299/264%20-%20Ugly%20Number%20II.md)
* [313. Super Ugly Number](/leetcode/300%20-%20399/313%20-%20Super%20Ugly%20Number.md)
* [668. Kth Smallest Number in Multiplication Table](/leetcode/600%20-%20699/668%20-%20Kth%20Smallest%20Number%20in%20Multiplication%20Table.md)
