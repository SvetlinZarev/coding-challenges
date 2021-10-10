# [32. Longest Valid Parentheses](https://leetcode.com/problems/longest-valid-parentheses/)

## Problem

Given a string containing just the characters `(` and `)`, find the length of
the longest valid (well-formed) parentheses substring.

#### Examples

```text
Input: s = "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()".
```

```text
Input: s = ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()".
```

## Solution

```rust
pub fn longest_valid_parentheses(s: String) -> i32 {
    let mut stack = vec![];
    let mut state = 0;

    for &x in s.as_bytes() {
        match x {
            b'(' => {
                state += 1;
                stack.push(0);
            }

            b')' => {
                state -= 1;
                if state < 0 {
                    state = 0;
                    if let Some(&top) = stack.last() {
                        if top >= 0 {
                            stack.push(-1);
                        }
                    }
                    continue;
                }

                let mut to_push = 2;
                while let Some(top) = stack.pop() {
                    if top < 0 {
                        stack.push(top);
                        break;
                    }

                    if top > 0 {
                        to_push += top;
                    }

                    if top == 0 {
                        break;
                    }
                }

                if let Some(top) = stack.last() {
                    if *top > 0 {
                        to_push += *top;
                        stack.pop();
                    }
                }
                stack.push(to_push);
            }
            _ => panic!(),
        }
    }

    stack.iter().copied().filter(|x| *x > 0).max().unwrap_or(0)
}
```