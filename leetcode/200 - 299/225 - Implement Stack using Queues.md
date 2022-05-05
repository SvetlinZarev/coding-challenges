# [225. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

## Problem

### Description

Implement a last-in-first-out (LIFO) stack using only two queues. The
implemented stack should support all the functions of a normal stack (`push`
, `top`, `pop`, and `empty`).

Implement the MyStack class:

* `void push(int x)` Pushes element `x` to the top of the stack.
* `int pop()` Removes the element on the top of the stack and returns it.
* `int top()` Returns the element on the top of the stack.
* `boolean empty()` Returns `true` if the stack is empty, `false` otherwise.

**Notes:**

* You must use only standard operations of a queue, which means that only push
  to back, `peek`/`pop` from front, size and is empty operations are valid.
* Depending on your language, the queue may not be supported natively. You may
  simulate a queue using a list or deque (double-ended queue) as long as you use
  only a queue's standard operations.

### Constraints

* `1 <= x <= 9`
* At most 100 calls will be made to `push`, `pop`, `top`, and `empty`.
* All the calls to `pop` and `top` are valid.

### Examples

```text
Input
["MyStack", "push", "push", "top", "pop", "empty"]
[[], [1], [2], [], [], []]
Output
[null, null, null, 2, 2, false]

Explanation
MyStack myStack = new MyStack();
myStack.push(1);
myStack.push(2);
myStack.top(); // return 2
myStack.pop(); // return 2
myStack.empty(); // return False
```

## Solutions

```rust
use std::collections::VecDeque;

struct MyStack {
    queue: VecDeque<i32>,
}

impl MyStack {
    fn new() -> Self {
        Self {
            queue: VecDeque::new(),
        }
    }

    fn push(&mut self, x: i32) {
        self.queue.push_back(x);
    }

    fn pop(&mut self) -> i32 {
        let mut q = VecDeque::new();

        while self.queue.len() > 1 {
            q.push_back(self.queue.pop_front().unwrap());
        }

        std::mem::swap(&mut self.queue, &mut q);
        q.pop_front().unwrap()
    }

    fn top(&mut self) -> i32 {
        let mut q = VecDeque::new();
        let mut top = 0;

        while let Some(x) = self.queue.pop_front() {
            q.push_back(x);
            if self.queue.len() == 0 {
                top = x;
            }
        }

        self.queue = q;
        top
    }

    fn empty(&self) -> bool {
        self.queue.is_empty()
    }
}
```
