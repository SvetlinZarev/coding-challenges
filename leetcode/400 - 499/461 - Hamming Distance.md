# [461. Hamming Distance](https://leetcode.com/problems/hamming-distance/)

## Problem

### Description

The Hamming distance between two integers is the number of positions at
which the corresponding bits are different.

Given two integers `x` and `y`, return the Hamming distance between them.

### Constraints

* `0 <= x, y <= 2^31 - 1`

### Examples

```text
Input: x = 1, y = 4
Output: 2
Explanation:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
The above arrows point to positions where the corresponding bits are different.
```

## Solution

```rust
pub fn hamming_distance(x: i32, y: i32) -> i32 {
    (x ^ y).count_ones() as i32
}
```
