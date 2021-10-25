# [155. Min Stack](https://leetcode.com/problems/min-stack/)

## Problem

Design a stack that supports `push`, `pop`, `top`, and retrieving the minimum
element in constant time.

Implement the `MinStack` class:

* `MinStack`() initializes the stack object.
* `void push(int val)` pushes the element `val` onto the stack.
* `void pop()` removes the element on the top of the stack.
* `int top()` gets the top element of the stack.
* `int getMin()` retrieves the minimum element in the stack.

#### Constraints

* `-2^31 <= val <= 2^31 - 1`
* Methods `pop`, `top` and `getMin` operations will always be called on
  non-empty stacks.
* At most `3 * 10^4` calls will be made to `push`, `pop`, `top`, and `getMin`.

#### Example

```text
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

## Solution

```text
struct MinStack {
    stack: Vec<(i32, i32)>,
}

impl MinStack {
    fn new() -> Self {
        MinStack { stack: Vec::new() }
    }

    fn push(&mut self, val: i32) {
        let mut min = val;

        if let Some(&(_, m)) = self.stack.last() {
            min = min.min(m);
        }

        self.stack.push((val, min));
    }

    fn pop(&mut self) {
        self.stack.pop();
    }

    fn top(&self) -> i32 {
        self.stack.last().copied().unwrap().0
    }

    fn get_min(&self) -> i32 {
        self.stack.last().copied().unwrap().1
    }
}
```