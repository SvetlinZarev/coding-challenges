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

### Using O(1) extra space

Because the indexes are unsigned integers I use `idx - 1` as the current index.
Thus, when `idx == 0`, we know that we have processed all characters

```rust
pub fn backspace_compare<S: AsRef<[u8]>, T: AsRef<[u8]>>(s: S, t: T) -> bool {
    let s = s.as_ref();
    let t = t.as_ref();

    let mut s_idx = s.len();
    let mut t_idx = t.len();

    // use OR because one of the strings can reach zero length before the other
    // although the remaining character would be deleted.
    // Example: s = "nzp#o#g", t = "b#nzp#o#g"
    while s_idx > 0 || t_idx > 0 {
        let mut skip = 0;
        while s_idx > 0 {
            if s[s_idx - 1] == b'#' {
                skip += 1;
            } else if skip > 0 {
                skip -= 1;
            } else {
                break;
            }

            s_idx -= 1;
        }

        let mut skip = 0;
        while t_idx > 0 {
            if t[t_idx - 1] == b'#' {
                skip += 1;
            } else if skip > 0 {
                skip -= 1;
            } else {
                break;
            }

            t_idx -= 1;
        }

        // If at least one of the indexes has reached 0,
        // then we should check if the indexes are both
        // zero. If they are are, then both strings are
        // equal to the empty string, otherwise the
        // strings are different.
        //
        // This check is performed at the end of the method
        if s_idx == 0 || t_idx == 0 {
            break;
        }

        s_idx -= 1;
        t_idx -= 1;

        if s[s_idx] != t[t_idx] {
            return false;
        }
    }

    s_idx == t_idx
}
```

Or with less code duplication:

```rust
pub fn backspace_compare<S: AsRef<[u8]>, T: AsRef<[u8]>>(s: S, t: T) -> bool {
    let s = s.as_ref();
    let t = t.as_ref();

    let mut s_idx = s.len();
    let mut t_idx = t.len();

    while s_idx > 0 || t_idx > 0 {
        s_idx = next_char_idx(s, s_idx);
        t_idx = next_char_idx(t, t_idx);

        // If at least one of the indexes has reached 0,
        // then we should check if the indexes are both
        // zero. If they are are, then both strings are
        // equal to the empty string, otherwise the
        // strings are different.
        //
        // This check is performed at the end of the method
        if s_idx == 0 || t_idx == 0 {
            break;
        }

        s_idx -= 1;
        t_idx -= 1;

        if s[s_idx] != t[t_idx] {
            return false;
        }
    }

    s_idx == t_idx
}

fn next_char_idx(s: &[u8], idx: usize) -> usize {
    let mut s_idx = idx;
    let mut skip = 0;

    while s_idx > 0 {
        if s[s_idx - 1] == b'#' {
            skip += 1;
        } else if skip > 0 {
            skip -= 1;
        } else {
            break;
        }

        s_idx -= 1;
    }

    s_idx
}
```