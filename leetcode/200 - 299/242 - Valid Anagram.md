# [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)

## Problem

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`,
and `false` otherwise.

#### Constraints

* `1 <= s.length, t.length <= 5 * 10^4`
* `s` and `t` consist of lowercase English letters.

#### Examples

```text
Input: s = "anagram", t = "nagaram"
Output: true
```

```text
Input: s = "rat", t = "car"
Output: false
```

## Solution

```rust
const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn is_anagram(s: String, t: String) -> bool {
    if s.len() != t.len() {
        return false;
    }

    let mut s_freq = [0i32; LETTERS];
    s.bytes()
        .map(|ch| (ch - b'a') as usize)
        .for_each(|ch| s_freq[ch] += 1);

    for ch in t.bytes().map(|ch| (ch - b'a') as usize) {
        if s_freq[ch] == 0 {
            return false;
        }

        s_freq[ch] -= 1;
    }

    true
}
```
