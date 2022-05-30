# [29. Divide Two Integers](https://leetcode.com/problems/divide-two-integers/)

## Problem

### Description

Given two integers `dividend` and `divisor`, divide two integers without using
multiplication, division, and mod operator.

The integer division should truncate toward zero, which means losing its
fractional part. For example, `8.345` would be truncated to `8`, and `-2.7335`
would be truncated to `-2`.

Return the quotient after dividing `dividend` by `divisor`.

Note: Assume we are dealing with an environment that could only store integers
within the 32-bit signed integer range: `[−2^31, 2^31 − 1]`. For this problem,
if the quotient is strictly greater than `2^31 - 1`, then return `2^31 - 1`, and
if the quotient is strictly less than `-2^31`, then return `-2^31`.

### Constraints

* `-2^31 <= dividend, divisor <= 2^31 - 1`
* `divisor != 0`

### Examples

```text
Input: dividend = 10, divisor = 3
Output: 3
Explanation: 10/3 = 3.33333.. which is truncated to 3.
```

```text
Input: dividend = 7, divisor = -3
Output: -2
Explanation: 7/-3 = -2.33333.. which is truncated to -2.
```

## Solutions

### Exponentiation by squaring

We use negative number because the range is larger with 1 number. We just have
to correct the sign at the end.

```rust
pub fn divide(dividend: i32, divisor: i32) -> i32 {
    assert_ne!(divisor, 0);

    // Invert the sign => if both have the same sign - use a
    // negative sign, else use a positive sign
    let sign = if dividend.signum() == divisor.signum() {
        -1
    } else {
        1
    };

    // Always use negative numbers
    let mut num = dividend;
    if num > 0 {
        num = -num;
    }

    // Always use negative numbers
    let mut div = divisor.abs();
    if div > 0 {
        div = -div;
    }

    let mut answer = 0i32;
    'l: while num <= div {
        // Always use negative numbers: i.e. we will be counting
        // towards i32::MIN instead of i32::MAX
        let mut x = -1;
        let mut d = div;

        while num <= d {
            answer += x;
            num -= d;
            d = match d.checked_add(d) {
                None => break 'l,
                Some(sum) => sum,
            };
            x += x;
        }
    }

    // Correct the sign of the answer. Because we've used negative numbers,
    // we may have to invert the sign. Also handle the corner case if `answer`
    // is `-2^31`, because the positive range is too small for it, so return
    // `2^31 - 1` instead
    if answer == i32::MIN && sign == -1 {
        return i32::MAX;
    }

    if sign == -1 {
        return -answer;
    }

    answer
}
```
