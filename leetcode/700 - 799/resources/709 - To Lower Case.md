# [709. To Lower Case](https://leetcode.com/problems/to-lower-case/)

## Problem

### Description

Given a string `s`, return the string after replacing every uppercase letter
with the same lowercase letter.

### Constraints

* `1 <= s.length <= 100`
* `s` consists of printable ASCII characters.

### Examples

```text
Input: s = "Hello"
Output: "hello"
```

```text
Input: s = "here"
Output: "here"
```

```text
Input: s = "LOVELY"
Output: "lovely"
```

## Solutions

```rust
pub fn to_lower_case(s: String) -> String {
    let mut l = String::with_capacity(s.len());

    for &ch in s.as_bytes() {
        let ch = if ch >= b'A' && ch <= b'Z' {
            ch - b'A' + b'a'
        } else {
            ch
        };
        l.push(ch as char);
    }

    l
}
```
