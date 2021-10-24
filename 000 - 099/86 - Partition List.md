# [86. Partition List](https://leetcode.com/problems/partition-list/)

## Problem

Given the head of a linked list and a value `x`, partition it such that all
nodes less than `x` come before nodes greater than or equal to `x`.

You must preserve the original relative order of the nodes in each of the two
partitions.

#### Constraints

* The number of nodes in the list is in the range `[0, 200]`.
* `-100 <= Node.val <= 100`
* `-200 <= x <= 200`

#### Examples

```text
Input: head = [1,4,3,2,5,2], x = 3
Output: [1,2,2,4,3,5]
```

```text
Input: head = [2,1], x = 2
Output: [1,2]
```

## Solution

```rust
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

pub fn partition(mut head: Option<Box<ListNode>>, x: i32) -> Option<Box<ListNode>> {
    let mut smaller = None;
    let mut larger = None;

    let mut tail_smaller = &mut smaller;
    let mut tail_larger = &mut larger;

    while let Some(mut node) = head.take() {
        head = node.next.take();

        if node.val < x {
            *tail_smaller = Some(node);
            tail_smaller = &mut tail_smaller.as_mut().unwrap().next;
        } else {
            *tail_larger = Some(node);
            tail_larger = &mut tail_larger.as_mut().unwrap().next;
        }
    }

    *tail_smaller = larger;

    smaller
}
```