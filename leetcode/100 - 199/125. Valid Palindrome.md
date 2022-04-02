# [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)

## Problem

### Description

A phrase is a palindrome if, after converting all uppercase letters into
lowercase letters and removing all non-alphanumeric characters, it reads the
same forward and backward. Alphanumeric characters include letters and numbers.

Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

### Constraints

* `1 <= s.length <= 2 * 10^5`
* `s` consists only of printable ASCII characters.

### Examples

```text
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
```

```text
Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
```

```text
Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.
```

## Solutions

### Two pointers

```rust
pub fn is_palindrome<S: AsRef<str>>(s: S) -> bool {
    assert!(s.as_ref().len() > 0);
    let s = s.as_ref().as_bytes();

    let mut a = 0;
    let mut b = s.len() - 1;

    while a < b {
        while a <= b && !s[a].is_ascii_alphanumeric() {
            a += 1;
        }

        while a <= b && !s[b].is_ascii_alphanumeric() {
            b -= 1;
        }

        // handle corner cases such as "a.", or all whitespace strings, etc
        if a >= b {
            return true;
        }

        if s[a].to_ascii_lowercase() != s[b].to_ascii_lowercase() {
            return false;
        }

        a += 1;
        b -= 1;
    }

    true
}
```

## Related Problems

* [680. Valid Palindrome II](/leetcode/600%20-%20699/680%20-%20Valid%20Palindrome%20II.md)