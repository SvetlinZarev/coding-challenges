# [1721. Swapping Nodes in a Linked List](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/)

## Problem

### Description

You are given the `head` of a linked list, and an integer `k`.

Return the `head` of the linked list after swapping the values of the `k`th node
from the beginning and the `k`th node from the end (the list is 1-indexed).

### Constraints

* The number of nodes in the list is `n`.
* `1 <= k <= n <= 10^5`
* `0 <= Node.val <= 100`

### Examples

#### Example 1

![image](resources/1721/ex1.jpg)

```text
Input: head = [1,2,3,4,5], k = 2
Output: [1,4,3,2,5]
```

#### Example 2

```text
Input: head = [7,9,6,6,7,8,3,0,9,5], k = 5
Output: [7,9,6,6,8,7,3,0,9,5]
```

## Solutions

### Convert the list to array

1. Convert the list array
2. Now it's very easy to swap the two values
3. Rebuild the list

### Without converting the list to array and the array back to list

The algorithm is:

1. Count the number of nodes in the list
2. Split the list in 3 parts
    1. Part 0: all the nodes before the first node to swap
    2. Part 1: all the nodes between the first and second nodes to swap
    3. Part 2: all the nodes after the second node to swap
3. Concatenate the 3 parts after swapping the two nodes

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

pub fn swap_nodes(head: Option<Box<ListNode>>, k: i32) -> Option<Box<ListNode>> {
    assert!(k > 0);
    // make it 0-indexed
    let k = (k - 1) as usize;

    let length = length(head.as_ref());
    if length == 0 {
        return head;
    }

    let node_idx_a = k.min(length - k - 1);
    let node_idx_b = k.max(length - k - 1);
    if node_idx_a == node_idx_b {
        return head;
    }

    // part-0 consists of all the nodes before the first node to swap
    let mut part_0 = head;
    let mut tail_0 = &mut part_0;

    for _ in 0..node_idx_a {
        tail_0 = &mut tail_0.as_mut().unwrap().next;
    }

    let mut node_1 = tail_0.take().unwrap();

    // part-1 consists of all the nodes after the first node to swap and before the second node to swap
    let mut part_1 = node_1.next.take();
    let mut tail_1 = &mut part_1;

    // we add + 1 because we have taken one node outside the loop
    for _ in node_idx_a + 1..node_idx_b {
        tail_1 = &mut tail_1.as_mut().unwrap().next;
    }

    let mut node_2 = tail_1.take().unwrap();
    node_1.next = node_2.next.take(); // part 2: all the remaining nodes
    *tail_1 = Some(node_1);
    node_2.next = part_1;
    *tail_0 = Some(node_2);

    part_0
}

fn length(head: Option<&Box<ListNode>>) -> usize {
    let mut node = head;
    let mut len = 0;

    while let Some(n) = node.take() {
        node = n.next.as_ref();
        len += 1;
    }

    len
}
```

**Note:** It's also possible to swap only the values instead of the nodes, in
that case we won't even need to split the list in parts:

1. Count the nodes
2. Iterate over the list & remember the first value. When the second value is
   reached, swap it with the remembered value
3. Iterate over the list once more and set the first value to the second value