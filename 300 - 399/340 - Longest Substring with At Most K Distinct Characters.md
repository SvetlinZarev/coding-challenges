# [340. Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)

## Problem

Given a string `S`, find the length of the longest substring `T` that contains at most `k` distinct characters.

#### Examples

```text
Input: S = "eceba" and k = 3
Output: 4
Explanation: T = "eceb"
```

```text
Input: S = "WORLD" and k = 4
Output: 4
Explanation: T = "WORL" or "ORLD"
```

## Solution

```rust
const ASCII_CHARACTERS: usize = (b'z' - b'a' + 1) as usize;

fn length_of_longest_substring_k_distinct<S: AsRef<str>>(s: S, k: u32) -> usize {
    let s = s.as_ref().as_bytes();
    if s.is_empty() {
        return 0;
    }

    let mut f = [0u32; ASCII_CHARACTERS];
    let mut from = 0;
    let mut to = 0;

    let mut distinct = 0;
    let mut best_len = 0;

    while to < s.len() {
        let ch = (s[to] - b'a') as usize;
        if f[ch] == 0 {
            distinct += 1;
        }
        f[ch] += 1;
        to += 1;

        while distinct > k {
            let rm_ch = (s[from] - b'a') as usize;
            if f[rm_ch] == 1 {
                distinct -= 1;
            }
            f[rm_ch] -= 1;
            from += 1;
        }

        best_len = best_len.max(to - from);
    }

    best_len
}
```

## Sources

* [Lint Code](https://www.lintcode.com/problem/longest-substring-with-at-most-k-distinct-characters)