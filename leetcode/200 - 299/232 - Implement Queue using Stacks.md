# [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

## Problem

Implement a first in first out (FIFO) queue using only two stacks. The
implemented queue should support all the functions of a normal queue (push,
peek, pop, and empty).

Implement the `MyQueue` class:

* `void push(int x)` Pushes element `x` to the back of the queue.
* `int pop()` Removes the element from the front of the queue and returns it.
* `int peek()` Returns the element at the front of the queue.
* `boolean empty()` Returns `true` if the queue is empty, `false` otherwise.

**Notes:**

* You must use only standard operations of a stack, which means only `push` to
  top, `peek`/`pop` from top, `size`, and `is empty` operations are valid.
* Depending on your language, the stack may not be supported natively. You may
  simulate a stack using a list or deque (double-ended queue) as long as you use
  only a stack's standard operations.

#### Constraints

* `1 <= x <= 9`
* At most 100 calls will be made to `push`, `pop`, `peek`, and `empty`.
* All the calls to `pop` and `peek` are valid.

## Solution

```rust
struct MyQueue {
    a: Vec<i32>,
    b: Vec<i32>,
}

impl MyQueue {
    fn new() -> Self {
        MyQueue {
            a: Vec::new(),
            b: Vec::new(),
        }
    }

    fn push(&mut self, x: i32) {
        self.a.push(x);
    }

    fn pop(&mut self) -> i32 {
        if let Some(x) = self.b.pop() {
            return x;
        }

        while let Some(x) = self.a.pop() {
            self.b.push(x);
        }

        self.b.pop().unwrap()
    }

    fn peek(&mut self) -> i32 {
        if let Some(&x) = self.b.last() {
            return x;
        }

        while let Some(x) = self.a.pop() {
            self.b.push(x);
        }

        self.b.last().copied().unwrap()
    }

    fn empty(&self) -> bool {
        self.a.is_empty() && self.b.is_empty()
    }
}
/**
 * Your MyQueue object will be instantiated and called as such:
 * let obj = MyQueue::new();
 * obj.push(x);
 * let ret_2: i32 = obj.pop();
 * let ret_3: i32 = obj.peek();
 * let ret_4: bool = obj.empty();
 */
```