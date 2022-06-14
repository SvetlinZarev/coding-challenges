# [583. Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/)

## Problem

### Description

Given two strings `word1` and `word2`, return the minimum number of steps
required to make `word1` and `word2` the same.

In one step, you can delete exactly one character in either string.

### Constraints

* `1 <= word1.length, word2.length <= 500`
* `word1` and `word2` consist of only lowercase English letters.

### Examples

```text
Input: word1 = "sea", word2 = "eat"
Output: 2
```

> Explanation: You need one step to make "sea" to "ea" and another step to
> make "eat" to "ea".

```text
Input: word1 = "leetcode", word2 = "etco"
Output: 4
```

## Solutions

### Longest Common Subsequence

The number of character to delete is equal to the sum of the lengths of `word1`
and `word2` minus twice the length (because it appears twice - i.e. once in each
word) of the longest common subsequence:

```rust
pub fn min_distance(word1: impl AsRef<str>, word2: impl AsRef<str>) -> i32 {
    let a = word1.as_ref().as_bytes();
    let b = word2.as_ref().as_bytes();

    let lcs = longest_common_subsequence(a, b);
    (a.len() + b.len() - 2 * lcs) as i32
}

fn longest_common_subsequence(a: &[u8], b: &[u8]) -> usize {
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

* [1143. Longest Common Subsequence](/leetcode/1100%20-%201199/1143%20-%20Longest%20Common%20Subsequence.md)
