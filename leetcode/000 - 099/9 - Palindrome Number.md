# [9. Palindrome Number](https://leetcode.com/problems/palindrome-number/)

## Problem

Given an integer `x`, return `true` if `x` is palindrome integer.

An integer is a palindrome when it reads the same backward as forward. For
example, `121` is palindrome while `123` is not.

## Solutions

### Reversing the number

```rust
 pub fn is_palindrome(x: i32) -> bool {
    x == reverse(x).abs()
}

pub fn reverse(x: i32) -> i32 {
    let mut remaining = x;
    let mut result = 0i32;

    while remaining != 0 {
        let digit = remaining % 10;
        remaining /= 10;

        result = match result.checked_mul(10) {
            Some(v) => v,
            None => return 0,
        };

        result = match result.checked_add(digit) {
            Some(v) => v,
            None => return 0,
        };
    }

    result
}
```