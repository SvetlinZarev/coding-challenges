# [316. Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/)

## Problem

Given a string `s`, remove duplicate letters so that every letter appears once
and only once. You must make sure your result is the **smallest in
lexicographical order** among all possible results.

#### Constraints

* `1 <= s.length <= 10^4`
* `s` consists of lowercase English letters.

#### Examples

```text
Input: s = "bcabc"
Output: "abc"
```

```text
Input: s = "cbacdcbc"
Output: "acdb"
```

## Solution

```rust
const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn remove_duplicate_letters(s: String) -> String {
    let mut freq = [0; LETTERS];
    let mut string = String::with_capacity(s.len());
    let mut stack = Vec::with_capacity(s.len().min(64));
    let mut used = 0u32; //bitflags for each letter in the alphabet

    for &ch in s.as_bytes() {
        freq[(ch - b'a') as usize] += 1;
    }

    for ch in s.as_bytes().iter().map(|&b| b - b'a') {
        freq[ch as usize] -= 1;

        while let Some(&top) = stack.last() {
            if top < ch || freq[top as usize] == 0 || used & (1u32 << ch) != 0 {
                break;
            }

            stack.pop();
            used ^= 1u32 << top;
        }

        if used & (1u32 << ch) == 0 {
            used |= 1u32 << ch;
            stack.push(ch);
        }
    }

    stack
        .iter()
        .map(|&b| (b + b'a') as char)
        .for_each(|b| string.push(b));
    string
}
```
