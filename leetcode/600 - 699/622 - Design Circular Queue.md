# [622. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)

## Problem

### Description

Design your implementation of the circular queue. The circular queue is a linear
data structure in which the operations are performed based on FIFO (First In
First Out) principle and the last position is connected back to the first
position to make a circle. It is also called "Ring Buffer".

One of the benefits of the circular queue is that we can make use of the spaces
in front of the queue. In a normal queue, once the queue becomes full, we cannot
insert the next element even if there is a space in front of the queue. But
using the circular queue, we can use the space to store new values.

Implementation the `MyCircularQueue` class:

* `MyCircularQueue(k)` Initializes the object with the size of the queue to
  be `k`.
* `int Front()` Gets the front item from the queue. If the queue is empty,
  return `-1`.
* `int Rear()` Gets the last item from the queue. If the queue is empty,
  return `-1`.
* `boolean enQueue(int value)` Inserts an element into the circular queue.
  Return `true` if the operation is successful.
* `boolean deQueue()` Deletes an element from the circular queue. Return `true`
  if the operation is successful.
* `boolean isEmpty()` Checks whether the circular queue is empty or not.
* `boolean isFull()` Checks whether the circular queue is full or not.

You must solve the problem without using the built-in queue data structure in
your programming language.

### Constraints

* `1 <= k <= 1000`
* `0 <= value <= 1000`
* At most 3000 calls will be made to `enQueue`, `deQueue`, `Front`, `Rear`
  , `isEmpty`, and `isFull`.

### Examples

```text
Input
["MyCircularQueue", "enQueue", "enQueue", "enQueue", "enQueue", "Rear", "isFull", "deQueue", "enQueue", "Rear"]
[[3], [1], [2], [3], [4], [], [], [], [4], []]
Output
[null, true, true, true, false, 3, true, true, true, 4]

Explanation
MyCircularQueue myCircularQueue = new MyCircularQueue(3);
myCircularQueue.enQueue(1); // return True
myCircularQueue.enQueue(2); // return True
myCircularQueue.enQueue(3); // return True
myCircularQueue.enQueue(4); // return False
myCircularQueue.Rear();     // return 3
myCircularQueue.isFull();   // return True
myCircularQueue.deQueue();  // return True
myCircularQueue.enQueue(4); // return True
myCircularQueue.Rear();     // return 4
```

## Solutions

```rust
struct MyCircularQueue {
    inner: Vec<i32>,
    front: usize,
    back: usize,
    used: usize,
}

impl MyCircularQueue {
    fn new(k: i32) -> Self {
        assert!(k > 0);

        Self {
            inner: vec![0; k as usize],
            front: 0,
            back: 0,
            used: 0,
        }
    }

    fn en_queue(&mut self, value: i32) -> bool {
        if self.is_full() {
            return false;
        }

        self.inner[self.back] = value;
        self.back = (self.back + 1) % self.inner.capacity();
        self.used += 1;

        true
    }

    fn de_queue(&mut self) -> bool {
        if self.is_empty() {
            return false;
        }

        self.front = (self.front + 1) % self.inner.capacity();
        self.used -= 1;

        true
    }

    fn front(&self) -> i32 {
        if self.used == 0 {
            return -1;
        }

        self.inner[self.front]
    }

    fn rear(&self) -> i32 {
        if self.used == 0 {
            return -1;
        }

        if self.back == 0 {
            return self.inner[self.inner.len() - 1];
        }

        self.inner[self.back - 1]
    }

    fn is_empty(&self) -> bool {
        self.used == 0
    }

    fn is_full(&self) -> bool {
        self.used == self.inner.capacity()
    }
}
```