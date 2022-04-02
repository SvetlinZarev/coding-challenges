# [680. Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/)

## Problem

### Description

Given a string `s`, return `true` if `s` can be palindrome after deleting at
most one character from it.

### Constraints

* `1 <= s.length <= 10^5`
* `s` consists of lowercase English letters.

### Examples

```text
Input: s = "aba"
Output: true
```

```text
Input: s = "abca"
Output: true
Explanation: You could delete the character 'c'.
```

```text
Input: s = "abc"
Output: false
```

## Solutions

## Two pointers

```rust
pub fn valid_palindrome<S: AsRef<str>>(s: S) -> bool {
    assert!(s.as_ref().len() > 0);

    let s = s.as_ref().as_bytes();
    let mut a = 0;
    let mut b = s.len() - 1;

    // find the indexes where s[a] != s[b]
    while a < b && s[a] == s[b] {
        a += 1;
        b -= 1;
    }

    // If there is no such index or one of the substrings is a palindrome, return true
    a >= b || is_palindrome(&s[a + 1..=b]) || is_palindrome(&s[a..b])
}

fn is_palindrome(s: &[u8]) -> bool {
    if s.len() <= 1 {
        return true;
    }

    let mut a = 0;
    let mut b = s.len() - 1;

    while a <= b && s[a] == s[b] {
        a += 1;
        b -= 1;
    }

    a > b
}
```

## Related Problems

* [125. Valid Palindrome](/leetcode/100%20-%20199/125.%20Valid%20Palindrome.md)