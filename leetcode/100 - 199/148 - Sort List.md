# [148. Sort List](https://leetcode.com/problems/sort-list/)

## Problem

Given the `head` of a linked list, return the list after sorting it in ascending
order.

#### Examples

```text
Input: head = [4,2,1,3]
Output: [1,2,3,4]
```

```text
Input: head = [-1,5,3,4,0]
Output: [-1,0,3,4,5]
```

```text
Input: head = []
Output: []
```

## Solution

### Merge sort on the list itself

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;

// Definition for singly-linked list by LeetCode
#[derive(PartialEq, Eq, Clone, Debug)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>,
}

impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode { next: None, val }
    }
}

struct NodeOrd(Box<ListNode>);

impl PartialEq for NodeOrd {
    fn eq(&self, other: &Self) -> bool {
        self.0.val == other.0.val
    }
}

impl Eq for NodeOrd {
    //
}

impl PartialOrd for NodeOrd {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(&other))
    }
}

impl Ord for NodeOrd {
    fn cmp(&self, other: &Self) -> Ordering {
        self.0.val.cmp(&other.0.val).reverse()
    }
}

pub fn sort_list(head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let (left, right) = split(head);
    if left.is_none() {
        return right;
    }
    if right.is_none() {
        return left;
    }

    let left = sort_list(left);
    let right = sort_list(right);
    merge(left, right)
}

fn split(head: Option<Box<ListNode>>) -> (Option<Box<ListNode>>, Option<Box<ListNode>>) {
    if head.is_none() {
        return (None, None);
    }

    let len = length(head.as_ref());
    let mut first = head;
    let mut tail = first.as_mut().unwrap();

    for _ in 1..len / 2 {
        tail = tail.next.as_mut().unwrap();
    }

    let second = tail.next.take();
    (first, second)
}

fn merge(mut l: Option<Box<ListNode>>, mut r: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut queue = BinaryHeap::with_capacity(2);
    if let Some(head) = l.take() {
        queue.push(NodeOrd(head));
    }
    if let Some(head) = r.take() {
        queue.push(NodeOrd(head));
    }

    let mut dummy = ListNode::new(0);
    let mut tail = &mut dummy.next;

    while let Some(NodeOrd(mut node)) = queue.pop() {
        if let Some(remaining) = node.next.take() {
            queue.push(NodeOrd(remaining));
        }

        tail = &mut tail.insert(node).next;
    }

    dummy.next.take()
}

fn length(mut head: Option<&Box<ListNode>>) -> usize {
    let mut count = 0;

    while let Some(h) = head.take() {
        head = h.next.as_ref();
        count += 1;
    }
    count
}
```