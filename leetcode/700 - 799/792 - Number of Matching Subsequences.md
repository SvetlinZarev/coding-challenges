# [792. Number of Matching Subsequences](https://leetcode.com/problems/number-of-matching-subsequences/)

## Problem

### Description

Given a string `s` and an array of strings `words`, return the number
of `words[i]` that is a subsequence of `s`.

A subsequence of a string is a new string generated from the original string
with some characters (can be none) deleted without changing the relative order
of the remaining characters.

* For example, `ace` is a subsequence of `abcde`.

### Constraints

* `1 <= s.length <= 5 * 10^4`
* `1 <= words.length <= 5000`
* `1 <= words[i].length <= 50`
* `s` and `words[i]` consist of only lowercase English letters.

### Examples

```text
Input: s = "abcde", words = ["a","bb","acd","ace"]
Output: 3
Explanation: There are three strings in words that are a subsequence of s: "a", "acd", "ace".
```

```text
Input: s = "dsahjpjauf", words = ["ahjpjau","ja","ahbwzgqnuk","tnmlanowax"]
Output: 2
```

## Solutions

### Bruteforce

```rust
pub fn num_matching_subseq<S: AsRef<str>>(s: S, words: Vec<String>) -> i32 {
    words
        .iter()
        .filter(|&x| is_sub_seq(s.as_ref().as_bytes(), x.as_bytes()))
        .count() as i32
}

fn is_sub_seq(s: &[u8], x: &[u8]) -> bool {
    let mut i = 0;
    let mut j = 0;

    while i < s.len() && j < x.len() && x.len() - j <= s.len() - i {
        if s[i] == x[j] {
            i += 1;
            j += 1;
            continue;
        }

        i += 1;
    }

    j == x.len()
}
```

### Indexing the `s` string

The idea is to create an index with the locations of each character. Then we can
use binary search to check if the character is present at an index greater than
the current index.

```rust
const LEN: usize = (b'z' - b'a' + 1) as usize;

pub fn num_matching_subseq<S: AsRef<str>>(s: S, words: Vec<String>) -> i32 {
    let mut index = vec![vec![]; LEN];
    for (idx, &ch) in s.as_ref().as_bytes().iter().enumerate() {
        let ch = (ch - b'a') as usize;
        index[ch].push(idx);
    }

    words
        .iter()
        .filter(|&x| is_sub_seq(&index, x.as_bytes()))
        .count() as i32
}

fn is_sub_seq(index: &[Vec<usize>], x: &[u8]) -> bool {
    let mut pos = 0;

    for ch in x.iter().copied() {
        let ch = (ch - b'a') as usize;
        let loc = index[ch].binary_search(&pos).unwrap_or_else(|e| e);

        // We are at position X in the string `s`, but the character `ch` does 
        // not appear after that position. Therefore the string `x` is not 
        // a subsequence of `s`
        if index[ch].len() <= loc {
            return false;
        }

        let idx = index[ch][loc];
        pos = idx + 1;
    }

    true
}
```