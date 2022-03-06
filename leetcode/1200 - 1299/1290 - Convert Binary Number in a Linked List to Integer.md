# [1290. Convert Binary Number in a Linked List to Integer](https://leetcode.com/problems/convert-binary-number-in-a-linked-list-to-integer/)

## Problem

### Description

Given `head` which is a reference node to a singly-linked list. The value of
each node in the linked list is either 0 or 1. The linked list holds the binary
representation of a number.

Return the decimal value of the number in the linked list.

### Constraints

* The Linked List is not empty.
* Number of nodes will not exceed 30.
* Each node's value is either 0 or 1.

### Examples

```text
Input: head = [1,0,1]
Output: 5
Explanation: (101) in base 2 = (5) in base 10
```

```text
Input: head = [0]
Output: 0
```

## Solutions

```rust
pub fn get_decimal_value(mut head: Option<Box<ListNode>>) -> i32 {
    let mut answer = 0;

    while let Some(mut node) = head.take() {
        head = node.next.take();
        answer = (answer << 1) | node.val;
    }
    
    answer
}
```

```rust
pub fn get_decimal_value(head: Option<Box<ListNode>>) -> i32 {
    let mut answer = 0;
    let mut head = head.as_ref();

    while let Some(node) = head.take() {
        head = node.next.as_ref();
        answer = (answer << 1) | node.val;
    }

    answer
}
```