# [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)

## Problem

### Description

Given a non-negative integer `x`, compute and return the square root of `x`.

Since the return type is an integer, the decimal digits are truncated, and only
the integer part of the result is returned.

Note: You are not allowed to use any built-in exponent function or operator,
such as `pow(x, 0.5)` or `x ** 0.5`.

### Constraints

* `0 <= x <= 2^31 - 1`

### Examples

```text
Input: x = 4
Output: 2
```

```text
Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since the decimal part is truncated, 2 is returned.
```

## Solutions

### Binary Search

```rust
use std::cmp::Ordering;

pub fn my_sqrt(x: i32) -> i32 {
    let mut lo = 1;
    let mut hi = x;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        match mid.checked_mul(mid) {
            None => hi = mid - 1,
            Some(val) => match val.cmp(&x) {
                Ordering::Equal => return mid,
                Ordering::Less => lo = mid + 1,
                Ordering::Greater => hi = mid - 1,
            },
        }
    }

    hi
}
```

## Related Problems

* [367. Valid Perfect Square](/leetcode/300%20-%20399/367%20-%20Valid%20Perfect%20Square.md)
* [633. Sum of Square Numbers](/leetcode/600%20-%20699/633%20-%20Sum%20of%20Square%20Numbers.md)