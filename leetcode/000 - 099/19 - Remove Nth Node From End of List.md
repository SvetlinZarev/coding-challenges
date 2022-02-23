# [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

## Problem

Given the head of a linked list, remove the nth node from the end of the list
and return its head.

#### Examples

```text
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

```text
Input: head = [1], n = 1
Output: []
```

```text
Input: head = [1,2], n = 1
Output: [1]
```

## Solutions

### Using a queue

Let's have a queue with capacity of `N`, then:

* While the number of elements in the queue is less than N, take the list head
  and put it in to the queue. The next element becomes the new list head
* Once the number of elements in the queue becomes N, pop the queue front before
  putting the new element at the back of the queue. Append the popped element to
  the tail of a "dummy" list
* Once there are no more elements in the original list, the front of the queue
  will hold the Nth element. Pop it and discard it, then append the rest to the
  list

```rust
pub fn remove_nth_from_end(mut head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
    let mut queue = VecDeque::with_capacity((n + 1) as usize);
    let mut dummy = ListNode::new(0);
    let mut tail = &mut dummy.next;

    while let Some(mut node) = head.take() {
        head = node.next.take();

        queue.push_back(node);
        if queue.len() == (n + 1) as usize {
            *tail = queue.pop_front();
            tail = &mut tail.as_mut().unwrap().next;
        }
    }

    // remove the Nth element
    queue.pop_front();

    // add the remaining elements back to the list
    while let Some(node) = queue.pop_front() {
        *tail = Some(node);
        tail = &mut tail.as_mut().unwrap().next;
    }

    dummy.next.take()
}
```

### Count the nodes

1. Iterate over the list and increment a counter for each new node
2. Subtract N from that counter - that's the position of the node we want to
   remove
3. Iterate to that position and remove the Nth node
4. Append the rest to the list

```rust
pub fn remove_nth_from_end(head: Option<Box<ListNode>>, n: i32) -> Option<Box<ListNode>> {
    let mut count = 0;
    let mut node = &head;
    while let Some(current) = node {
        count += 1;
        node = &current.next;
    }

    let mut list = ListNode::new(0);
    list.next = head;
    let mut list = Some(Box::new(list));

    let mut position = 0;
    let mut tail = &mut list;
    while let Some(next) = tail {
        if position == count - n {
            let mut skip = next.next.take();
            if let Some(skip) = skip {
                next.next = skip.next;
            }

            break;
        }

        position += 1;
        tail = &mut next.next;
    }

    list.unwrap().next.take()
}
```

### Using two pointers

That's hard (is it possible ?) to implement in rust, due to the aliasing rules.
The idea is to have two pointers, N nodes apart. When the rightmost pointer
reaches the end, the left pointer will point to the Nth node, so we can remove
it without additional memory as in the queue based solution, and with only 1
iteration unlike the solution with the counter