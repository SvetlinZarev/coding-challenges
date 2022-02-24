# [172. Factorial Trailing Zeroes](https://leetcode.com/problems/factorial-trailing-zeroes/)

## Problem

### Description

Given an integer `n`, return the number of trailing zeroes in `n!`.

Note that `n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1`.

### Constraints

* `0 <= n <= 10^4`

### Examples

```text
Input: n = 3
Output: 0
Explanation: 3! = 6, no trailing zero.
```

```text
Input: n = 5
Output: 1
Explanation: 5! = 120, one trailing zero.
```

```text
Input: n = 0
Output: 0
```

## Solution

### Observations

* A trailing zero can be created only by multiplying a `10`,
  i.e. `10 * 10 -> 100`).
* A `10` can be created my multiplying `5` to any even number
* Every second number is even
* Every fifth number is a multiple of `5`
* Therefore, we need only to count the number of `5`s in the factoriel
* Some numbers, such as `25` are made of several `5`s: `5 * 5 == 25`
  , `5 * 5 * 5 == 125`, etc. We need to count all those `5`s separately.

### `O(n)` solution

```rust
pub fn trailing_zeroes(n: i32) -> i32 {
    let mut fives = 0;
    for mut x in 0..n + 1 {
        while x > 0 && x % 5 == 0 {
            x /= 5;
            fives += 1;
        }
    }
    fives
}
```

### Optimized `O(n)` solution

We need to process only the multiples of 5, not all numbers. Every 5th number is
a multiple of 5.

```rust
pub fn trailing_zeroes(n: i32) -> i32 {
    let mut fives = 0;
    for mut x in (5..n + 1).step_by(5) {
        while x > 0 && x % 5 == 0 {
            x /= 5;
            fives += 1;
        }
    }
    fives
}
```

### Logarithmic solution

We can speed tup the algorithm by processing multiple multiples of 5 at once.
For instance lets calculate the trailing zeroes for `125!`:

* The first division by 5 will yield 25, because we have 25 numbers that
  contain `5` at least once:
    * 5, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95,
      100, 105, 110, 115, 120, 125
* The second iteration will yield `5` because we have 5 numbers which
  contain `5 * 5`:
    * 25, 50, 75, 100, 125
* The third iteration will yield `1`, because only one number is made
  of `5 *5 * 5`:
    * 125

```rust
pub fn trailing_zeroes(n: i32) -> i32 {
    let mut fives = 0;
    let mut div = 5;

    while div <= n {
        fives += n / div;
        div *= 5;
    }

    fives
}
```