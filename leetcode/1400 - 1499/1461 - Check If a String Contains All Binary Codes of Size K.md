# [1461. Check If a String Contains All Binary Codes of Size K](https://leetcode.com/problems/check-if-a-string-contains-all-binary-codes-of-size-k/)

## Problem

### Description

Given a binary string `s` and an integer `k`, return `true` if every binary code
of length `k` is a substring of `s`. Otherwise, return `false`.

### Constraints

* `1 <= s.length <= 5 * 10^5`
* `s[i]` is either `0` or `1`.
* `1 <= k <= 20`

### Examples

```text
Input: s = "00110110", k = 2
Output: true
Explanation: The binary codes of length 2 are "00", "01", "10" and "11". They can be all found as substrings at indices 0, 1, 3 and 2 respectively.
```

```text
Input: s = "0110", k = 1
Output: true
Explanation: The binary codes of length 1 are "0" and "1", it is clear that both exist as a substring. 
```

```text
Input: s = "0110", k = 2
Output: false
Explanation: The binary code "00" is of length 2 and does not exist in the array.
```

## Solutions

### Collecting all unique substrings of length `k` in a set

Time complexity: `O(k*n)`

```rust
use std::collections::HashSet;

pub fn has_all_codes<S: AsRef<str>>(s: S, k: i32) -> bool {
    assert!(k > 0);

    let distinct_substrings = s
        .as_ref()
        .as_bytes()
        .windows(k as usize)
        .collect::<HashSet<_>>()
        .len();

    // the count of binary numbers of length `k` is `2.pow(k)` 
    // which is equal to `2 << k`
    distinct_substrings == 2usize.pow(k as u32)
}
```
