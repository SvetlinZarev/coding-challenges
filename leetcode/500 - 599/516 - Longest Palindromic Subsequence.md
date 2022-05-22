# [516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)

## Problem

### Description

Given a string `s`, find the longest palindromic subsequence's length in `s`.

A subsequence is a sequence that can be derived from another sequence by
deleting some or no elements without changing the order of the remaining
elements.

### Constraints

* `1 <= s.length <= 1000`
* `s` consists only of lowercase English letters.

### Examples

```text
Input: s = "bbbab"
Output: 4
Explanation: One possible longest palindromic subsequence is "bbbb".
```

```text
Input: s = "cbbd"
Output: 2
Explanation: One possible longest palindromic subsequence is "bb".
```

## Solutions

### Recursion + memoization

```rust
use std::collections::HashMap;

pub fn longest_palindrome_subseq(string: String) -> i32 {
    let s = string.as_bytes();
    let mut cache = HashMap::new();
    process_sub_sequences(s, &mut cache, 0, s.len() - 1)
}

fn process_sub_sequences(
    s: &[u8],
    cache: &mut HashMap<(usize, usize), i32>,
    l: usize,
    r: usize,
) -> i32 {
    if l > r {
        return 0;
    }

    if l == r {
        return 1;
    }

    if let Some(&len) = cache.get(&(l, r)) {
        return len;
    }

    let mut length = 0;
    if s[l] == s[r] {
        length = 2 + process_sub_sequences(s, cache, l + 1, r - 1);
    } else {
        let a = process_sub_sequences(s, cache, l + 1, r);
        let b = process_sub_sequences(s, cache, l, r - 1);
        length = a.max(b);
    }

    cache.insert((l, r), length);
    length
}
```

## Related Problems

* [5. Longest Palindromic Substring](/leetcode/000%20-%20099/5%20-%20Longest%20Palindromic%20Substring.md)
* [647. Palindromic Substrings](/leetcode/600%20-%20699/647%20-%20Palindromic%20Substrings.md)
