# [856. Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/)

## Problem

### Description

Given a balanced parentheses string `s`, return the score of the string.

The score of a balanced parentheses string is based on the following rule:

* `()` has score 1.
* `AB` has score `A + B`, where `A` and `B` are balanced parentheses strings.
* `(A)` has score `2 * A`, where `A` is a balanced parentheses string.

### Constraints

* `2 <= s.length <= 50`
* `s` consists of only `(` and `)`.
* `s` is a balanced parentheses string.

### Examples

```text
Input: s = "()"
Output: 1
```

```text
Input: s = "(())"
Output: 2
```

```text
Input: s = "()()"
Output: 2
```

## Solutions

### Using a stack

The task is very similar to writing a reverse polish notation calculator

```rust
enum Token {
    Boundary,
    Value(i32),
}

pub fn score_of_parentheses(s: String) -> i32 {
    let mut stack = vec![];

    for ch in s.chars() {
        match ch {
            '(' => stack.push(Token::Boundary),
            ')' => match stack.pop() {
                None => panic!("invalid input: unbalanced brackets"),
                Some(Token::Boundary) => stack.push(Token::Value(1)),
                Some(Token::Value(x)) => {
                    let mut sum = x;
                    while let Some(last) = stack.pop() {
                        match last {
                            Token::Boundary => break,
                            Token::Value(x) => sum += x,
                        }
                    }
                    stack.push(Token::Value(sum * 2));
                }
            },
            ch => panic!("invalid input: {}", ch),
        }
    }

    stack
        .into_iter()
        .map(|x| match x {
            Token::Boundary => panic!("the stack must contain only values"),
            Token::Value(x) => x,
        })
        .sum()
}
```

### Using a stack

```rust
pub fn score_of_parentheses(s: String) -> i32 {
    let mut stack = vec![0]; // init with the current score

    for ch in s.chars() {
        match ch {
            '(' => stack.push(0),
            ')' => {
                let a = stack.pop().unwrap();
                let b = stack.pop().unwrap();
                stack.push(b + 1.max(2 * a));
            }
            _ => unreachable!(),
        }
    }

    stack.pop().unwrap()
}
```

### Counting cores

```rust
pub fn score_of_parentheses(s: String) -> i32 {
    let mut balance = 0;
    let mut answer = 0;

    for idx in 0..s.as_bytes().len() {
        match s.as_bytes()[idx] {
            b'(' => balance += 1,
            b')' => {
                balance -= 1;
                if s.as_bytes()[idx - 1] == b'(' {
                    answer += 1 << balance;
                }
            }
            _ => unreachable!(),
        }
    }

    answer
}
```