# [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

## Problem

Given a signed 32-bit integer `x`, return `x` with its digits reversed. If
reversing x causes the value to go outside the signed 32-bit integer
range `[-2^31, 2^31 - 1]`, then return 0.

Assume the environment does not allow you to store 64-bit integers (signed or
unsigned).

#### Constraints

* `-2^31 <= x <= 2^31 - 1`

#### Examples

```text
Input: x = 123
Output: 321
```

```text
Input: x = -123
Output: -321
```

```text
Input: x = 120
Output: 21
```

## Solution

```rust
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