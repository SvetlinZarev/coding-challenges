# [725. Split Linked List in Parts](https://leetcode.com/problems/split-linked-list-in-parts/)

## Problem

Given the `head` of a singly linked list and an integer `k`, split the linked
list into `k` consecutive linked list parts.

The length of each part should be as equal as possible: no two parts should have
a size differing by more than one. This may lead to some parts being `null`.

The parts should be in the order of occurrence in the input list, and parts
occurring earlier should always have a size greater than or equal to parts
occurring later.

Return an array of the `k` parts.

#### Hints

> If there are N nodes in the list, and k parts, then every part has N/k
> elements, except the first N%k parts have an extra one.

#### Constraints

* The number of nodes in the list is in the range `[0, 1000]`.
* `0 <= Node.val <= 1000`
* `1 <= k <= 50`

#### Examples

```text
Input: head = [1,2,3], k = 5
Output: [[1],[2],[3],[],[]]

Explanation:
The first element output[0] has output[0].val = 1, output[0].next = null.
The last element output[4] is null, but its string representation as a ListNode is [].
```

```text
Input: head = [1,2,3,4,5,6,7,8,9,10], k = 3
Output: [[1,2,3,4],[5,6,7],[8,9,10]]

Explanation:
The input has been split into consecutive parts with size difference at most 1, 
and earlier parts are a larger size than the later parts.
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


pub fn split_list_to_parts(
    mut head: Option<Box<ListNode>>,
    mut groups: i32,
) -> Vec<Option<Box<ListNode>>> {
    let mut count = 0;
    let mut h = head.as_ref();
    while let Some(node) = h {
        h = node.next.as_ref();
        count += 1;
    }

    let mut result = Vec::with_capacity(groups as usize);
    while groups > 0 {
        let mut chunk = count / groups;
        if count % groups != 0 {
            chunk += 1;
        }
        count -= chunk;
        groups -= 1;

        let mut h = &mut head;
        while chunk > 0 {
            chunk -= 1;
            if let Some(node) = h {
                h = &mut node.next;
            }
        }

        let mut next_head = h.take();
        std::mem::swap(&mut head, &mut next_head);
        result.push(next_head);
    }

    result
}
```