# [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

## Problem

### Description

Given a string `s`, return the number of palindromic substrings in it.

A string is a palindrome when it reads the same backward as forward.

A substring is a contiguous sequence of characters within the string.

### Constraints

* `1 <= s.length <= 1000`
* `s` consists of lowercase English letters.

### Examples

```text
Input: s = "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
```

```text
Input: s = "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

## Solutions

### Start from single character and expand

```rust
pub fn count_substrings(s: String) -> i32 {
    let s = s.as_bytes();
    let mut answer = 0;

    // expand from every single character in the source string
    for idx in 0..s.len() {
        // odd length palindromes
        answer += extend_palindrome(s, idx, idx);

        // even length palindromes
        answer += extend_palindrome(s, idx, idx + 1);
    }

    answer
}

fn extend_palindrome(s: &[u8], left: usize, right: usize) -> i32 {
    let (mut l, mut r) = (left, right);
    let mut count = 0;

    while r < s.len() && s[l] == s[r] {
        count += 1;

        if l == 0 {
            break;
        }

        l -= 1;
        r += 1;
    }

    count
}
```