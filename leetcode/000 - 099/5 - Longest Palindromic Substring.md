# [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

## Problem

### Description

Given a string `s`, return the longest palindromic substring in `s`.

### Constraints

* `1 <= s.length <= 1000`
* `s` consist of only digits and English letters.

### Examples

```text
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

```text
Input: s = "cbbd"
Output: "bb"
```

## Solutions

### Expand the palindrome starting from each character of `s`

```rust
pub fn longest_palindrome(string: String) -> String {
    let s = string.as_bytes();

    let mut answer = "";

    for idx in 0..s.len() {
        // odd length palindromes
        let (a, b) = expand_palindrome(s, idx, idx + 1);

        // even length palindromes
        let (c, d) = expand_palindrome(s, idx, idx + 2);

        if (b - a) > answer.len() {
            answer = &string[a..b];
        }

        if (d - c) > answer.len() {
            answer = &string[c..d];
        }
    }

    answer.to_owned()
}

// * `l` is inclusive
// * `r` is exclusive
fn expand_palindrome(s: &[u8], l: usize, r: usize) -> (usize, usize) {
    assert!(r - l <= 2);

    if l >= r || r > s.len() {
        return (0, 0);
    }

    let mut left = l;
    let mut right = r - 1;

    // the initial pair was not a palindrome
    if s[left] != s[right] {
        return (0, 0);
    }

    loop {
        if left == 0 {
            break;
        }

        if right == s.len() - 1 {
            break;
        }

        if s[left - 1] != s[right + 1] {
            break;
        }

        left -= 1;
        right += 1;
    }

    (left, right + 1)
}
```

## Related Problems

* [647. Palindromic Substrings](/leetcode/600%20-%20699/647%20-%20Palindromic%20Substrings.md)
