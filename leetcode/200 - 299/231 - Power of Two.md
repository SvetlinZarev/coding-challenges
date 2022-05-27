# [231. Power of Two](https://leetcode.com/problems/power-of-two/)

## Problem

### Description

Given an integer `n`, return `true` if it is a power of two. Otherwise,
return `false`.

An integer `n` is a power of two, if there exists an integer `x` such
that `n == 2^x`

### Constraints

* `-2^31 <= n <= 2^31 - 1`

### Examples

```text
Input: n = 1
Output: true
Explanation: 20 = 1
```

```text
Input: n = 16
Output: true
Explanation: 24 = 16
```

```text
Input: n = 3
Output: false
```

## Solutions

In binary form, a number is a power of two only if it has a single bit set
to `1`.

### Remove the last set bit

The following algorithm sets the least significant `1` bit to `0`. Thus, if `n`
is a power of two, the result will be `0`

```rust
pub fn is_power_of_two(n: i32) -> bool {
    n > 0 && (n & (n - 1)) == 0
}
```

### Count ones

Languages such as rust expose a method similar to `number::count_ones()`, which
returns the number of `1` bits. Just check if it's equal to `1`