# [263. Ugly Number](https://leetcode.com/problems/ugly-number/)

## Problem

An ugly number is a positive integer whose prime factors are limited to `2`, `3`
, and `5`.

Given an integer `n`, return true if `n` is an ugly number.

#### Constraints

* `-2^31 <= n <= 2^31 - 1`

#### Examples

```text
Input: n = 14
Output: false
Explanation: 14 is not ugly since it includes the prime factor 7.
```

```text
Input: n = 1
Output: true
Explanation: 1 has no prime factors, therefore all of its prime factors are limited to 2, 3, and 5.
```

## Solution

```rust
pub fn is_ugly(n: i32) -> bool {
    if n == 0 {
        return false;
    }

    let mut n = n;

    while n % 2 == 0 {
        n /= 2;
    }

    while n % 3 == 0 {
        n /= 3;
    }

    while n % 5 == 0 {
        n /= 5;
    }

    n == 1
}
```

## Related Problems

* [264. Ugly Number II](/200%20-%20299/264%20-%20Ugly%20Number%20II.md)
* [313. Super Ugly Number](/300%20-%20399/313%20-%20Super%20Ugly%20Number.md)
