# [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)

## Problem

### Description

Given two strings `text1` and `text2`, return the length of their longest common
subsequence. If there is no common subsequence, return `0`.

A subsequence of a string is a new string generated from the original string
with some characters (can be none) deleted without changing the relative order
of the remaining characters.

* For example, `ace` is a subsequence of `abcde`.

A common subsequence of two strings is a subsequence that is common to both
strings.

### Constraints

* `1 <= text1.length, text2.length <= 1000`
* `text1` and `text2` consist of only lowercase English characters.

### Examples

```text
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
```

```text
Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.
```

```text
Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0.
```

## Solutions

### Naive (exponential - `O(2^n)` - solution)

```rust
pub fn longest_common_subsequence(text1: impl AsRef<str>, text2: impl AsRef<str>) -> i32 {
    lcs(
        text1.as_ref().as_bytes(),
        text1.as_ref().len(),
        text2.as_ref().as_bytes(),
        text2.as_ref().len(),
    ) as i32
}

fn lcs(a: &[u8], idx_a: usize, b: &[u8], idx_b: usize) -> usize {
    if idx_a == 0 || idx_b == 0 {
        return 0;
    }

    if a[idx_a - 1] == b[idx_b - 1] {
        return 1 + lcs(a, idx_a - 1, b, idx_b - 1);
    }

    let x = lcs(a, idx_a - 1, b, idx_b);
    let y = lcs(a, idx_a, b, idx_b - 1);
    x.max(y)
}
```

### Top-down DP

```rust

pub fn longest_common_subsequence(text1: impl AsRef<str>, text2: impl AsRef<str>) -> i32 {
    let text_1 = text1.as_ref().as_bytes();
    let text_2 = text2.as_ref().as_bytes();
    let mut cache = vec![vec![usize::MAX; text_2.len()]; text_1.len()];

    lcs(&mut cache, text_1, text_1.len(), text_2, text_2.len()) as i32
}

fn lcs(cache: &mut [Vec<usize>], a: &[u8], idx_a: usize, b: &[u8], idx_b: usize) -> usize {
    if idx_a == 0 || idx_b == 0 {
        return 0;
    }

    if cache[idx_a - 1][idx_b - 1] != usize::MAX {
        return cache[idx_a - 1][idx_b - 1];
    }

    cache[idx_a - 1][idx_b - 1] = if a[idx_a - 1] == b[idx_b - 1] {
        1 + lcs(cache, a, idx_a - 1, b, idx_b - 1)
    } else {
        let x = lcs(cache, a, idx_a - 1, b, idx_b);
        let y = lcs(cache, a, idx_a, b, idx_b - 1);
        x.max(y)
    };

    cache[idx_a - 1][idx_b - 1]
}
```

### Bottom-up DP

```rust
pub fn longest_common_subsequence(text1: impl AsRef<str>, text2: impl AsRef<str>) -> i32 {
    lcs(text1.as_ref().as_bytes(), text2.as_ref().as_bytes()) as i32
}

fn lcs(a: &[u8], b: &[u8]) -> usize {
    let mut dp = vec![vec![0; b.len() + 1]; a.len() + 1];

    for i in 1..=a.len() {
        for j in 1..=b.len() {
            dp[i][j] = if a[i - 1] == b[j - 1] {
                dp[i - 1][j - 1] + 1
            } else {
                dp[i - 1][j].max(dp[i][j - 1])
            }
        }
    }

    dp[a.len()][b.len()]
}
```

## Related Problems

* [583. Delete Operation for Two Strings](/leetcode/500%20-%20599/583%20-%20Delete%20Operation%20for%20Two%20Strings.md)
* [1048. Longest String Chain](/leetcode/1000%20-%201099/1048%20-%20Longest%20String%20Chain.md)
