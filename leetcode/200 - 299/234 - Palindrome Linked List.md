# [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

## Problem

### Description

Given the `head` of a singly linked list, return `true` if it is a palindrome.

### Constraints

* The number of nodes in the list is in the range `[1, 10^5]`.
* `0 <= Node.val <= 9`

### Examples

```text
Input: head = [1,2,2,1]
Output: true
```

```text
Input: head = [1,2]
Output: false
```

## Solutions

### Reverse the linked list at the middle

* Space complexity: `O(1)`
* Time complexity: `O(n)`

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

pub fn is_palindrome(head: Option<Box<ListNode>>) -> bool {
    let (h1, h2) = split_in_half(head);
    let h2 = reverse(h2);

    let mut a = h1.as_ref();
    let mut b = h2.as_ref();

    while a.is_some() && b.is_some() {
        let x = a.unwrap();
        let y = b.unwrap();

        if x.val != y.val {
            return false;
        }

        a = x.next.as_ref();
        b = y.next.as_ref();
    }

    true
}

fn split_in_half(list: Option<Box<ListNode>>) -> (Option<Box<ListNode>>, Option<Box<ListNode>>) {
    let cut_at = (length(list.as_ref()) + 1) / 2;

    let mut first = list;
    let mut tail = first.as_mut();

    // count from one, because `tail` already contains a node
    for _ in 1..cut_at {
        tail = tail.and_then(|node| node.next.as_mut());
    }

    let second = tail.and_then(|node| node.next.take());

    (first, second)
}

fn length(head: Option<&Box<ListNode>>) -> u32 {
    let mut count = 0;
    let mut node = head;

    while let Some(n) = node.take() {
        node = n.next.as_ref();
        count += 1;
    }

    count
}

fn reverse(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut list = None;

    while let Some(mut node) = head.take() {
        head = node.next.take();
        node.next = list;
        list = Some(node);
    }

    list
}
```

## Related Problems

* [206. Reverse Linked List](206%20-%20Reverse%20Linked%20List.md)
* [876. Middle of the Linked List](/leetcode/800%20-%20899/876%20-%20Middle%20of%20the%20Linked%20List.md)

