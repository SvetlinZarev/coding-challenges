# [844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare/)

## Problem

Given two strings `s` and `t`, return `true` if they are equal when both are
typed into empty text editors. `#` means a backspace character.

Note that after backspacing an empty text, the text will continue empty.

#### Follow up:

Can you solve it in O(n) time and O(1) space?

#### Constraints

* `1 <= s.length, t.length <= 200`
* `s` and `t` only contain lowercase letters and `#` characters.

#### Examples

```text
Input: s = "ab#c", t = "ad#c"
Output: true
Explanation: Both s and t become "ac".
```

```text
Input: s = "ab##", t = "c#d#"
Output: true
Explanation: Both s and t become "".
```

```text
Input: s = "a##c", t = "#a#c"
Output: true
Explanation: Both s and t become "c".
```

```text
Input: s = "a#c", t = "b"
Output: false
Explanation: s becomes "c" while t becomes "b".
```

## Solution

### With O(s + t) additional space

```rust
pub fn backspace_compare(s: String, t: String) -> bool {
    let s = s.as_bytes();
    let t = t.as_bytes();

    let mut a = Vec::with_capacity(s.len());
    let mut b = Vec::with_capacity(t.len());

    for ch in s.iter().copied() {
        if ch == b'#' {
            a.pop();
        } else {
            a.push(ch);
        }
    }

    for ch in t.iter().copied() {
        if ch == b'#' {
            b.pop();
        } else {
            b.push(ch);
        }
    }

    a == b
}
```