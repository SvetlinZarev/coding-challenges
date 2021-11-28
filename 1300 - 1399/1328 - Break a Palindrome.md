# [1328. Break a Palindrome](https://leetcode.com/problems/break-a-palindrome/)

## Problem

Given a palindromic string of lowercase English letters `palindrome`, replace
exactly one character with any lowercase English letter so that the resulting
string is not a palindrome and that it is the lexicographically smallest one
possible.

Return the resulting string. If there is no way to replace a character to make
it not a palindrome, return an empty string.

A string `a` is lexicographically smaller than a string `b` (of the same length)
if in the first position where `a` and `b` differ, `a` has a character strictly
smaller than the corresponding character in `b`. For example, `abcc` is
lexicographically smaller than `abcd` because the first position they differ is
at the fourth character, and `c` is smaller than `d`.

#### Constraints

* `1 <= palindrome.length <= 1000`
* `palindrome` consists of only lowercase English letters.

#### Examples

```text
Input: palindrome = "abccba"
Output: "aaccba"
Explanation: There are many ways to make "abccba" not a palindrome, such as "zbccba", "aaccba", and "abacba".
Of all the ways, "aaccba" is the lexicographically smallest.
```

```text
Input: palindrome = "a"
Output: ""
Explanation: There is no way to replace a single character to make "a" not a palindrome, so return an empty string.
```

```text
Input: palindrome = "aa"
Output: "ab"
```

```text
Input: palindrome = "aba"
Output: "abb"
```

#### Hints

1. How to detect if there is impossible to perform the replacement? Only when
   the length = 1.
2. Change the first non `a` character to `a`
3. What if the string has only `a` ?
4. Change the last character to `b`

## Solution

```rust
pub fn break_palindrome(mut string: String) -> String {
    if string.len() <= 1 {
        string.truncate(0);
        return string;
    }

    let s = unsafe { string.as_bytes_mut() };
    let len = s.len();

    for ch in s.iter_mut().take(len / 2) {
        if *ch != b'a' {
            *ch = b'a';
            return string;
        }
    }

    s[len - 1] += 1; // all characters were `a`. Increment `a` to `b`
    string
}
```