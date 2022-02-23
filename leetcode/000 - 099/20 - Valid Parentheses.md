# [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

## Problem

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']',
determine if the input string is valid.

An input string is valid if:

* Open brackets must be closed by the same type of brackets.
* Open brackets must be closed in the correct order.

## Solution

```rust
pub fn is_valid(s: String) -> bool {
    let mut stack = vec![];

    for &x in s.as_bytes() {
        match x {
            b'(' => stack.push(b')'),
            b'[' => stack.push(b']'),
            b'{' => stack.push(b'}'),
            b')' | b']' | b'}' => {
                if Some(x) != stack.pop() {
                    return false;
                }
            }

            _ => unreachable!("Invalid character: {}", x as char),
        }
    }

    stack.is_empty()
}
```