# [61. Rotate List](https://leetcode.com/problems/rotate-list/)

## Problem

Given the head of a linked list, rotate the list to the right by `k` places.

![example](/100%20-%20199/resources/61/rotate1.jpg)

#### Constraints

* The number of nodes in the list is in the range `[0, 500]`.
* `-100 <= Node.val <= 100`
* `0 <= k <= 2 * 10^9`

### Examples

```text
Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]
```

```text
Input: head = [0,1,2], k = 4
Output: [2,0,1]
```

## Solution

```rust

pub fn rotate_right(head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
    if k == 0 {
        return head;
    }

    let len = length(head.as_ref());
    if len == 0 {
        return head;
    }

    let rotation = (k as usize) % len;
    if rotation == 0 {
        return head;
    }

    let mut first = head;
    let mut second = None;

    // find which part of the list should be moved to the front
    let mut cut_point = len - rotation;
    let mut list = first.as_mut();
    while let Some(next) = list.take() {
        cut_point -= 1;
        if cut_point == 0 {
            second = next.next.take();
            break;
        }

        list = next.next.as_mut();
    }
    drop(list);

    // move the second part of the list to the front
    let mut list = second.as_mut();
    while let Some(mut node) = list.take() {
        if node.next.is_none() {
            node.next = first;
            break;
        }

        list = node.next.as_mut();
    }

    second
}

fn length(mut head: Option<&Box<ListNode>>) -> usize {
    let mut len = 0;
    let mut list = head.take();

    while let Some(next) = list.take() {
        list = next.next.as_ref();
        len += 1;
    }

    len
}
```