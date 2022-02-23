# [767. Reorganize String](https://leetcode.com/problems/reorganize-string/)

## Problem

Given a string `s`, rearrange the characters of `s` so that any two adjacent
characters are not the same.

Return any possible rearrangement of `s` or return `""` if not possible.

#### Constraints

* `1 <= s.length <= 500`
* `s` consists of lowercase English letters.

#### Examples

```text
Input: s = "aab"
Output: "aba"
```

```text
Input: s = "aaab"
Output: ""
```

## Solution

```rust
use std::collections::BinaryHeap;

const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn reorganize_string(s: String) -> String {
    let mut string = String::with_capacity(s.len());

    let mut characters = [0; LETTERS];
    for ch in s.as_bytes().iter().copied() {
        characters[(ch - b'a') as usize] += 1;
    }

    let mut heap = BinaryHeap::with_capacity(LETTERS);
    for (idx, count) in characters.iter().copied().enumerate() {
        if count > 0 {
            heap.push((count, (idx as u8 + b'a') as char));
        }
    }

    let mut last = None;
    while let Some((mut count, ch)) = heap.pop() {
        string.push(ch);
        count -= 1;

        if let Some(last) = last.take() {
            heap.push(last);
        }

        if count > 0 {
            last = Some((count, ch));
        }
    }

    if last.is_some() {
        string.truncate(0);
    }

    string
}
```
