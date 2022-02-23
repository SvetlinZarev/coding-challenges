# [328. Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)

## Problem

Given the head of a singly linked list, group all the nodes with odd indices
together followed by the nodes with even indices, and return the reordered list.

The first node is considered odd, and the second node is even, and so on.

Note that the relative order inside both the even and odd groups should remain
as it was in the input.

You must solve the problem in O(1) extra space complexity and O(n) time
complexity.

#### Constraints

* `n == number of nodes in the linked list`
* `0 <= n <= 10^4`
* `-10^6 <= Node.val <= 10^6`

#### Examples

```text
Input: head = [1,2,3,4,5]
Output: [1,3,5,2,4]
```

```text
Input: head = [2,1,3,5,6,4,7]
Output: [2,3,6,7,1,5,4]
```

## Solution

```rust
pub fn odd_even_list(mut head: Option<Box<ListNode>>) -> Option<Box<ListNode>> {
    let mut odd = None;
    let mut even = None;

    let mut odd_tail = &mut odd;
    let mut even_tail = &mut even;

    let mut is_even = false;
    while let Some(mut node) = head.take() {
        head = node.next.take();

        if is_even {
            *even_tail = Some(node);
            even_tail = &mut even_tail.as_mut().unwrap().next;
        } else {
            *odd_tail = Some(node);
            odd_tail = &mut odd_tail.as_mut().unwrap().next;
        }

        is_even = !is_even;
    }

    *odd_tail = even;
    odd
}
```