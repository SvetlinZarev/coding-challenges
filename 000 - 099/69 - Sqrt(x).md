# [69. Sqrt(x)](https://leetcode.com/problems/sqrtx/)

## Problem

Given a non-negative integer `x`, compute and return the square root of `x`.

Since the return type is an integer, the decimal digits are truncated, and only
the integer part of the result is returned.

Note: You are not allowed to use any built-in exponent function or operator,
such as `pow(x, 0.5)` or `x ** 0.5`.

#### Constraints

* `0 <= x <= 2^31 - 1`

#### Examples

```text
Input: x = 4
Output: 2
```

```text
Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since the decimal part is truncated, 2 is returned.
```

## Solution

```rust
pub fn my_sqrt(x: i32) -> i32 {
    let mut lo = 1;
    let mut hi = x;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        match mid.checked_mul(mid) {
            None => {
                hi = mid - 1;
                continue;
            }

            Some(val) => {
                if val == x {
                    return mid;
                } else if mid * mid > x {
                    hi = mid - 1;
                } else {
                    lo = mid + 1;
                }
            }
        }
    }

    hi
}
```