# [201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/)

## Problem

Given two integers `left` and `right` that represent the range `[left, right]`,
return the bitwise AND of all numbers in this range, **inclusive**.

#### Constraints

* `0 <= left <= right <= 2^31 - 1`

#### Examples

```text
Input: left = 5, right = 7
Output: 4
```

```text
Input: left = 0, right = 0
Output: 0
```

```text
Input: left = 1, right = 2147483647
Output: 0
```

## Solutions

### Naive solution

The naive solution is to iterate over the range while the result is not zero.

* Time: 280 ms

```rust
pub fn range_bitwise_and(left: i32, right: i32) -> i32 {
    let mut result = right;

    for x in left..right {
        result &= x;
        if result == 0 {
            break;
        }
    }

    result
}
```

### Divide the range in two

Let's take an 8-bit integer as an example. The idea is to get the left half to
not have overlapping bits with the right half:

```text
0000_1111
&
1111_0000
```

Thus, we can have an earlier exit in case the AND of the range is 0

* Time: 4ms

```rust
pub fn range_bitwise_and(left: i32, right: i32) -> i32 {
    if left == right {
        return left;
    }

    let mid = (right - left) / 2 + left;
    let mut lp = mid;
    let mut rp = mid;

    let mut result = left & right;

    while result != 0 && lp > left && rp < right {
        result &= lp & rp;
        lp -= 1;
        rp += 1;
    }

    result
}
```

### Bit Hacks

**Observations:**

* For any range where `smaller` < `larger`, the AND of the least significant bit
  will always be 0, because it changes between 0 and 1 on each consecutive
  number in the range.
* The leftmost 1 bits in `larger` that are 0 in `smaller` will be 0 after the
  AND operation.
* In order to set a certain bit to 1, one must go through the following
  sequence: `000 -> 001 -> 010 -> 011 -> 100`, therefore if `larger` has a most
  significant bit that is not present in smaller, the AND of the range will be
    0.

Therefore, the AND of the range will be those bits that are the same both
in `smaller` and `larger` and have never changed in the whole range - i.e. the
sequence of consecutive bits from left to right (i.e.from the most significant
to the least significant bit) that is present in both `smaller` and `larger`

* Time 4 ms

```rust
pub fn range_bitwise_and(mut smaller: i32, mut larger: i32) -> i32 {
    let mut shifted = 0;

    // we'll do at most 32 iterations because it's a 32 bit number
    while smaller != larger {
        smaller >>= 1;
        larger >>= 1;

        // count how many times we have shifted it to the right
        // we'll need it to restore the common bits to their 
        // original positions later
        shifted += 1;
    }

    // restore the common bits to their original positions
    smaller << shifted
}
```

### Brian Kernighan's algorithm

The reasoning is the same as in the "bit hacks" solution. The difference here is
that the Kernighan's algorithm drops the least significant 1 bit, thus the
runtime is proportional to the number of set bits instead of the total number of
bits in the number.

Or said in other words - we keep dropping the least significant bit of `larger`
until it becomes equal or smaller than `smaller`. We do this because if you want
to set a bit to `1` you must have gone through setting the previous (less
significant) bits to `0` in the smaller numbers in the range, thus when ANDed,
the end result would have been 0. Therefore, we keep dropping least significant
bits until `large` contains only the common prefix

* Time 4ms

```rust
pub fn range_bitwise_and(smaller: i32, larger: i32) -> i32 {
    let mut result = larger;

    while result > smaller {
        result = result & (result - 1);
    }

    result
}
```