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

Runs in 111ms in the online judge and uses 22 MB at the time of writing

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
    // which is equal to `1 << k`
    distinct_substrings == 2usize.pow(k as u32)
}
```

### Rolling Hash

Time complexity: `O(n)`

Runs in 4ms in the online judge and uses 3.3 MB at the time of writing

Instead of computing the hash from scratch for each substring, we can *update*
it. Because we are working strings representing binary numbers, we can just map
the substring to a number, i.e. the string `"1001"` becomes the number `9`. We
can do that mapping in a *rolling* fashion by using bit-shifts. Just append the
new bit to the right (LSB) and remove the bit at the left (MSB). Thus we can
process all substrings in a linear time

```rust

pub fn has_all_codes<S: AsRef<str>>(s: S, k: i32) -> bool {
    assert!(k >= 1 && k <= 20);

    let s = s.as_ref().as_bytes();
    if s.len() < k as usize {
        return false;
    }

    let mut visited = vec![false; (1 << k) as usize];

    let mut count = 0;
    let mut hash = 0;
    let mask = (1 << k) - 1;

    // initialize the hash
    for idx in 0..k as usize {
        // roll the hash to the left, making space for the new character
        hash = hash << 1;

        // set the newly freed bit to 0 or 1 depending of the character value
        hash |= (s[idx] == b'1') as usize;
    }
    visited[hash] = true;
    count += 1;

    for idx in k as usize..s.len() {
        // roll the hash to the left, making space for the new character
        hash = hash << 1;

        // set the newly freed bit to 0 or 1 depending of the character value
        hash |= (s[idx] == b'1') as usize;

        // clear all bits with positions larger than `k - 1`
        hash &= mask;

        // Check if we have already processed that substring
        if !visited[hash] {
            visited[hash] = true;
            count += 1;
        }
    }

    count == 1 << k
}
```

## Related Problems

* [187. Repeated DNA Sequences](/leetcode/100%20-%20199/187%20-%20Repeated%20DNA%20Sequences.md)]
