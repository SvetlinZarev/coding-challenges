# [1019. Next Greater Node In Linked List](https://leetcode.com/problems/next-greater-node-in-linked-list/)

## Problem

You are given the `head` of a linked list with `n` nodes.

For each node in the list, find the value of the next greater node. That is, for
each node, find the value of the first node that is next to it and has a
strictly larger value than it.

Return an integer array `answer` where `answer[i]` is the value of the next
greater node of the `i`th node (1-indexed). If the `i`th node does not have a
next greater node, set `answer[i] = 0`.

#### Constraints

* The number of nodes in the list is `n`.
* `1 <= n <= 10^4`
* `1 <= Node.val <= 10^9`

#### Examples

![diagram](resources/1019/linkedlistnext1.jpg)

```text
Input: head = [2,1,5]
Output: [5,5,0]
```

![diagram](resources/1019/linkedlistnext2.jpg)

```text
Input: head = [2,7,4,3,5]
Output: [7,0,5,5,0]
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


pub fn next_larger_nodes(head: Option<Box<ListNode>>) -> Vec<i32> {
    let mut stack = Vec::with_capacity(32);
    let mut result = Vec::with_capacity(512);

    let mut head = head;
    let mut idx = 0;
    while let Some(mut node) = head.take() {
        head = node.next.take();

        while let Some(&(top, pos)) = stack.last() {
            if top >= node.val {
                break;
            }

            stack.pop();
            result[pos] = node.val;
        }

        stack.push((node.val, idx));
        result.push(0);
        idx += 1;
    }

    result
}
```

## Topics

* Monotonic stack