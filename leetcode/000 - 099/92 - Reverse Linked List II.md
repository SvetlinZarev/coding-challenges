# [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)

## Problem

Given the head of a singly linked list and two integers `left` and `right`
where `left <= right`, reverse the nodes of the list from position `left` to
position `right`, and return the reversed list.

#### Constraints

* The number of nodes in the list is `n`.
* `1 <= n <= 500`
* `-500 <= Node.val <= 500`
* `1 <= left <= right <= n`

#### Examples

```text
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
```

## Solutions

### In-place solution

```rust
pub fn reverse_between(
    mut head: Option<Box<ListNode>>,
    left: i32,
    right: i32,
) -> Option<Box<ListNode>> {
    let mut list = ListNode::new(0);
    let mut tail = &mut list.next;

    // append the nodes from 0..left in the same order
    for _ in 1..left {
        let mut node = head.unwrap();
        head = node.next.take();

        *tail = Some(node);
        tail = &mut tail.as_mut().unwrap().next;
    }

    // append the nodes from left..=right in reverse order
    for _ in left..=right {
        let mut node = head.unwrap();
        head = node.next.take();

        node.next = tail.take();
        *tail = Some(node);
    }

    // find the new `tail`
    while let Some(ref mut node) = tail {
        tail = &mut node.next;
    }

    // append the remaining nodes 
    *tail = head;
    
    list.next.take()
}
```