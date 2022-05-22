# [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)

## Problem

### Description

The Fibonacci numbers, commonly denoted `F(n)` form a sequence, called the
Fibonacci sequence, such that each number is the sum of the two preceding ones,
starting from `0` and `1`. That is,

* F(0) = 0, F(1) = 1
* F(n) = F(n - 1) + F(n - 2), for n > 1.

Given `n`, calculate `F(n)`.

### Constraints

* `0 <= n <= 30`

### Examples

```text
Input: n = 2
Output: 1
Explanation: F(2) = F(1) + F(0) = 1 + 0 = 1.
```

```text
Input: n = 3
Output: 2
Explanation: F(3) = F(2) + F(1) = 1 + 1 = 2.
```

```text
Input: n = 4
Output: 3
Explanation: F(4) = F(3) + F(2) = 2 + 1 = 3.
```

## Solution

```rust
pub fn fib(n: i32) -> i32 {
    if n == 0 {
        return 0;
    }

    let mut f1 = 0;
    let mut f2 = 1;

    for _ in 1..n {
        let f = f1 + f2;
        f1 = f2;
        f2 = f;
    }

    f2
}
```
