# [459. Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/)

## Problem

### Description

Given a string `s`, check if it can be constructed by taking a substring of it
and appending multiple copies of the substring together.

### Constraints

* `1 <= s.length <= 10^4`
* `s` consists of lowercase English letters.

### Examples

```text
Input: s = "abab"
Output: true
Explanation: It is the substring "ab" twice.
```

```text
Input: s = "aba"
Output: false
```

```text
Input: s = "abcabcabcabc"
Output: true
Explanation: It is the substring "abc" four times or the substring "abcabc" twice.
```

## Solutions

### Bruteforce

```rust
pub fn repeated_substring_pattern(s: String) -> bool {
    if s.len() <= 1 {
        return false;
    }

    // Try to split the input string to N substrings of equal length, 
    // then check if all those pieces are the same.
    for size in (1..s.len() / 2 + 1).rev() {
        if s.len() % size != 0 {
            continue;
        }

        let found = s
            .as_bytes()
            .windows(size * 2)
            .step_by(size)
            .all(|w| &w[..size] == &w[size..]);

        if found {
            return true;
        }
    }

    false
}
```

### Clever trick

Append the string to itself and remove the first and last characters. If the
original string is a substring of the new string, then it's made of a repeating
pattern:

```rust
pub fn repeated_substring_pattern(s: String) -> bool {
    s.repeat(2)[1..s.len() * 2 - 1].contains(&s)
}
```

### KMP Algorithm

TODO :)

## Resources

* [Knuth–Morris–Pratt algorithm](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)