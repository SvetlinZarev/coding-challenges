# [338. Counting Bits](https://leetcode.com/problems/counting-bits/)

## Problem

### Description

Given an integer `n`, return an array `ans` of length `n + 1` such that for
each `i` (`0 <= i <= n`), `ans[i]` is the number of 1's in the binary
representation of `i`.

### Constraints

* `0 <= n <= 10^5`

### Examples

```text
Input: n = 2
Output: [0,1,1]
Explanation:
0 --> 0
1 --> 1
2 --> 10
```

```text
Input: n = 5
Output: [0,1,1,2,1,2]
Explanation:
0 --> 0
1 --> 1
2 --> 10
3 --> 11
4 --> 100
5 --> 101
```

#### Follow up

* It is very easy to come up with a solution with a runtime of O(n log n). Can
  you do it in linear time O(n) and possibly in a single pass?
* Can you do it without using any built-in function (i.e.,
  like `__builtin_popcount` in C++)?

## Solutions

### Using `i32::count_ones()`

```rust
pub fn count_bits(mut n: i32) -> Vec<i32> {
    let mut bits = Vec::with_capacity(n as usize + 1);

    for i in 0..=n {
        bits.push(i.count_ones() as i32);
    }

    bits
}
```