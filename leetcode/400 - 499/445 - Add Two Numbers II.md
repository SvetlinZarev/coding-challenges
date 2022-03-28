# [445. Add Two Numbers II](https://leetcode.com/problems/add-two-numbers-ii/)

## Problem

### Description

You are given two non-empty linked lists representing two non-negative integers.
The most significant digit comes first and each of their nodes contains a single
digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the
number 0 itself.

### Constraints

* The number of nodes in each linked list is in the range `[1, 100]`.
* `0 <= Node.val <= 9`
* It is guaranteed that the list represents a number that does not have leading
  zeros.

### Examples

![image](resources/445/ex1.jpg)

```text
Input: l1 = [7,2,4,3], l2 = [5,6,4]
Output: [7,8,0,7]
```

```text
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [8,0,7]
```

```text
Input: l1 = [0], l2 = [0]
Output: [0]
```

## Solutions

#### Algorithm

1. Reverse the two input lists in order to get the least significant digits
   first
2. Sum the two lists
3. Reverse the result in order to get the most significant digits first

#### LeetCode API

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
```

#### Solution

```rust

pub fn add_two_numbers(
    l1: Option<Box<ListNode>>,
    l2: Option<Box<ListNode>>,
) -> Option<Box<ListNode>> {
    let mut l1 = reverse(l1);
    let mut l2 = reverse(l2);

    let mut head = None;
    let mut tail = &mut head;
    let mut carry = 0;

    loop {
        let value = match (l1.take(), l2.take()) {
            (None, None) => break,
            (Some(mut a), Some(mut b)) => {
                l1 = a.next.take();
                l2 = b.next.take();

                a.val + b.val + carry
            }
            (Some(mut a), None) => {
                l1 = a.next.take();

                a.val + carry
            }
            (None, Some(mut b)) => {
                l2 = b.next.take();

                b.val + carry
            }
        };

        carry = value / 10;

        let node = Box::new(ListNode::new(value % 10));
        tail = &mut tail.insert(node).next;
    }

    if carry > 0 {
        *tail = Some(Box::new(ListNode::new(carry)));
    }

    reverse(head)
}

fn reverse(mut list: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut head = None;

    while let Some(mut node) = list.take() {
        list = node.next.take();
        node.next = head;
        head = Some(node);
    }

    head
}
```

## Related Problems

* [2. Add Two Numbers](/leetcode/000%20-%20099/2%20-%20Add%20Two%20Numbers.md)