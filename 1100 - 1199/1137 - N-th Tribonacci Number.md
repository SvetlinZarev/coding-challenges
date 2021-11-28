# [1137. N-th Tribonacci Number](https://leetcode.com/problems/n-th-tribonacci-number/)

## Problem

The Tribonacci sequence Tn is defined as follows:

`T0 = 0`, `T1 = 1`, `T2 = 1`, and `Tn+3 = Tn + Tn+1 + Tn+2` for `n >= 0`.

Given `n`, return the value of `Tn`.

#### Constraints

* `0 <= n <= 37`
* The answer is guaranteed to fit within a 32-bit integer,
  ie. `answer <= 2^31 - 1`.

#### Examples

```text
Input: n = 4
Output: 4
Explanation:
T_3 = 0 + 1 + 1 = 2
```

```text
Input: n = 25
Output: 1389537
```

## Solution

````rust
pub fn tribonacci(n: i32) -> i32 {
    assert!(n >= 0);

    match n {
        0 => 0,
        1 => 1,
        2 => 1,
        n => {
            let mut a = 0;
            let mut b = 1;
            let mut c = 1;

            for _ in 3..=n {
                let t = a + b + c;
                a = b;
                b = c;
                c = t;
            }

            c
        }
    }
}
````