# [392. Is Subsequence](https://leetcode.com/problems/is-subsequence/)

## Problem

### Description

Given two strings `s` and `t`, return `true` if `s` is a subsequence of `t`, or `
false` otherwise.

A subsequence of a string is a new string that is formed from the original
string by deleting some (can be none) of the characters without disturbing the
relative positions of the remaining characters. (i.e., `ace` is a subsequence
of `abcde` while `aec` is not).

### Constraints

* `0<= s.length <= 100`
* `0 <= t.length <= 10^4`
* `s` and `t` consist only of lowercase English letters.

### Examples

```text
Input: s = "abc", t = "ahbgdc"
Output: true
```

```text
Input: s = "axc", t = "ahbgdc"
Output: false
```

### Follow-up

Suppose there are lots of incoming `s`, say `s1, s2, ..., sk` where `k >= 10^9`,
and you want to check one by one to see if `t` has its subsequence. In this
scenario, how would you change your code?

## Solutions

### Solution to the main problem

```rust
pub fn is_subsequence(s: String, t: String) -> bool {
    let s = s.as_bytes();
    let t = t.as_bytes();

    if s.len() > t.len() {
        return false;
    }

    let mut idxs = 0;
    let mut idxt = 0;

    while idxs < s.len() && idxt < t.len() {
        if s[idxs] == t[idxt] {
            idxs += 1;
        }

        idxt += 1;
    }

    idxs == s.len()
}
```