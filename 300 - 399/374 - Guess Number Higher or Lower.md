# [374. Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)

## Problem

We are playing the Guess Game. The game is as follows:

I pick a number from `1` to `n`. You have to guess which number I picked.

Every time you guess wrong, I will tell you whether the number I picked is
higher or lower than your guess.

You call a **pre-defined API** `int guess(int num)`, which returns 3 possible
results:

* `-1`: The number I picked is lower than your guess (i.e. `pick < num`).
* `1`: The number I picked is higher than your guess (i.e. `pick > num`).
* `0`: The number I picked is equal to your guess (i.e. `pick == num`).

Return the number that I picked.

#### Constraints

* `1 <= pick <= n`

## Solution

We are given the following function:

```rust
fn guess(n: i32) -> i32 {
    // returns either -1, 0, or +1
}
```

Note that I have removed the completely unnecessary "unsafeness" from the given
signatures!

```rust
fn guessNumber(n: i32) -> i32 {
    let mut lo = 0;
    let mut hi = n;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        let hint = guess(mid);
        if hint < 0 {
            hi = mid - 1;
        } else if hint > 0 {
            lo = mid + 1;
        } else {
            return mid;
        }
    }

    return -1;
}
```