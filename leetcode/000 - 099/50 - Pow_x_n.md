# [50. Pow(x, n)](https://leetcode.com/problems/powx-n/)

## Problem

Implement `pow(x, n)`, which calculates `x` raised to the power `n`

#### Constraints

* `-100.0 < x < 100.0`
* `-2^31 <= n <= 2^31-1`
* `-10^4 <= x^n <= 10^4`

#### Examples

```text
Input: x = 2.00000, n = -2
Output: 0.25000
Explanation: 2^-2 = 1/2^2 = 1/4 = 0.25
```

## Solution

```rust
pub fn my_pow(x: f64, n: i32) -> f64 {
    if n == 0 {
        return 1.0;
    }

    let abs_x = x.abs();
    let abs_n = (n as i64).abs();
    let mut result = abs_x;

    let mut power = 1;
    while power < abs_n {
        let rem = abs_n - power;
        let mut val = abs_x;

        let mut l = 1;
        while l < rem / 2 {
            l *= 2;
            val *= val;
        }

        power += l;
        result *= val;
    }

    if abs_n & 1 == 1 {
        result *= x.signum();
    }

    if n > 0 {
        result
    } else {
        1.0 / result
    }
}
```