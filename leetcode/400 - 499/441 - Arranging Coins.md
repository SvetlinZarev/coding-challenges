# [441. Arranging Coins](https://leetcode.com/problems/arranging-coins/)

## Problem

You have `n` coins and you want to build a staircase with these coins. The staircase 
consists of `k` rows where the `i`th row has exactly `i` coins. The last row of the 
staircase may be incomplete.

Given the integer `n`, return the number of complete rows of the staircase you will build.


#### Constraints

* `1 <= n <= 2^31 - 1`


#### Examples
  
```text
Input: n = 5
Output: 2
Explanation: Because the 3rd row is incomplete, we return 2.
```

```text
Input: n = 8
Output: 3
Explanation: Because the 4th row is incomplete, we return 3.
```

## Solutions

### Naive solution

```rust
pub fn arrange_coins(mut n: i32) -> i32 {
    let mut rows = 1;

    while n >= rows {
        n -= rows;
        rows += 1;
    }

    rows - 1
}
```

### Binary Search

If we mirror the staircase, we get a rectangle:

```text
OOO
XOO
XXO
XXX
```

In other words, if we have X rows of the staircase, we will have `X * (X + 1)` 
area of the rectangle. This means that in order to make a complete staircase
with `X` rows, we will need `X * (X + 1) / 2` coins.

Thus, we can run a binary search using the formula above. We stop the search 
when we find the biggest number that is less than `N`:

```rust
pub fn arrange_coins(mut n: i32) -> i32 {
    if n < 2 {
        // 0 => 0 rows,
        // 1 => 1 row
        return n;
    }

    // use usize because the "coins" formula may cause an overflow
    let mut lo = 0usize;
    let mut hi = n as usize;

    while lo <= hi {
        // the `K` from the problem statement, i.e. the number of rows
        let mid = (hi - lo) / 2 + lo;

        let coins = mid * (mid + 1) / 2;
        if coins == n as usize {
            return mid as i32;
        }

        if coins > n as usize{
            if mid == 0 {
                break;
            }
            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }

    return hi as i32;
}
```
