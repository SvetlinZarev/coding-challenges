# [1952. Three Divisors](https://leetcode.com/problems/three-divisors/)

## Problem

Given an integer `n`, return `true` if `n` has exactly three positive divisors.
Otherwise, return `false`.

An integer `m` is a divisor of `n` if there exists an integer `k` such
that `n = k * m`.

#### Constraints

* `1 <= n <= 10^4`

#### Examples

```text
Input: n = 2
Output: false
Explantion: 2 has only two divisors: 1 and 2.
```

```text
Input: n = 4
Output: true
Explantion: 4 has three divisors: 1, 2, and 4.
```

## Solution

All numbers are divisible by `1` and by themselves. Thus we need to fine just
one more divisor

```rust
    pub fn is_three(n: i32) -> bool {
    let mut divisors = 0;

    for i in 2..=n / 2 {
        if n % i == 0 {
            divisors += 1;
            if divisors > 1 {
                break;
            }
        }
    }

    divisors == 1
}
```