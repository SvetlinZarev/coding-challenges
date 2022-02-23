# [633. Sum of Square Numbers](https://leetcode.com/problems/sum-of-square-numbers/)

## Problem

Given a non-negative integer `c`, decide whether there are two integers `a`
and `b` such that `a^2 + b^2 = c`.

#### Constraints

* `0 <= c <= 2^31 - 1`

#### Examples

````text
Input: c = 5
Output: true
Explanation: 1 * 1 + 2 * 2 = 5
````

```text
Input: c = 3
Output: false
```

```text
Input: c = 4
Output: true
```

```text
Input: c = 2
Output: true
```

```text
Input: c = 1
Output: true
```

## Solution

### Using Binary Search

We can use binary search to look for a number in the range `[c - a^2]`, which is
the square root of `c - a^2`:

```rust
pub fn judge_square_sum(c: i32) -> bool {
    if c.count_ones() <= 1 {
        // Zero, and any power of two are valid solutions
        return true;
    }

    let limit = (c as f64).sqrt() as i32;

    for i in 0..limit {
        let a = i * i;  // i^2
        let b = c - a;  // c - i^2

        let mut lo = i;
        //let mut hi = limit;
        let mut hi = (b as f64).sqrt() as i32;


        while lo <= hi {
            let mid = lo + (hi - lo) / 2;
            let mids = mid * mid;

            if b == mids { // mid is the square root of `c - i^2`
                return true;
            }

            if b < mids {
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        }
    }

    false
}
```