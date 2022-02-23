# [387. First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/submissions/)

## Problem

Given a string `s`, find the first non-repeating character in it and return its
index. If it does not exist, return `-1`.

#### Constraints

* `1 <= s.length <= 10^5`
* `s` consists of only lowercase English letters.

#### Examples

```text
Input: s = "leetcode"
Output: 0
```

```text
Input: s = "loveleetcode"
Output: 2
```

```text
Input: s = "aabb"
Output: -1
```

## Solution

```rust
const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn first_uniq_char(s: String) -> i32 {
    let mut freq = [0; LETTERS];
    s.as_bytes()
        .iter()
        .copied()
        .map(|ch| (ch - b'a') as usize)
        .for_each(|ch| {
            freq[ch] += 1;
        });

    s.as_bytes()
        .iter()
        .copied()
        .map(|ch| (ch - b'a') as usize)
        .enumerate()
        .find(|&(idx, ch)| freq[ch] == 1)
        .map(|(idx, ch)| idx as i32)
        .unwrap_or(-1)
}
```