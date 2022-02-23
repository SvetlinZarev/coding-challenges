# [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)

## Problem

You are given two non-empty linked lists representing two non-negative integers.
The digits are stored in reverse order, and each of their nodes contains a
single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the
number 0 itself.

#### Examples

```text
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

```text
Input: l1 = [0], l2 = [0]
Output: [0]
```

```text
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
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

pub fn add_two_numbers(
    l1: Option<Box<ListNode>>,
    l2: Option<Box<ListNode>>,
) -> Option<Box<ListNode>> {
    let mut carry = 0;
    let mut a = &l1;
    let mut b = &l2;

    let mut helper_head = ListNode::new(0);
    let mut tail = &mut helper_head;

    while a.is_some() || b.is_some() {
        let a0 = a;
        let b0 = b;
        let sum = match (a0, b0) {
            (Some(x), Some(y)) => {
                a = &x.next;
                b = &y.next;
                x.val + y.val + carry
            }
            (Some(x), None) => {
                a = &x.next;
                x.val + carry
            }
            (None, Some(y)) => {
                b = &y.next;
                y.val + carry
            }
            (None, None) => carry,
        };

        carry = sum / 10;
        tail.next = Some(Box::new(ListNode::new(sum % 10)));
        tail = tail.next.as_mut().unwrap();
    }

    if carry > 0 {
        tail.next = Some(Box::new(ListNode::new(carry)));
    }

    helper_head.next
}
```