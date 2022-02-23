# [383. Ransom Note](https://leetcode.com/problems/ransom-note/)

## Problem

Given two stings `ransomNote` and `magazine`, return `true` if `ransomNote` can
be constructed from `magazine` and `false` otherwise.

Each letter in `magazine` can only be used once in `ransomNote`.

#### Constraints

* `1 <= ransomNote.length, magazine.length <= 10^5`
* `ransomNote` and `magazine` consist of lowercase English letters.

#### Examples

```text
Input: ransomNote = "a", magazine = "b"
Output: false
```

```text
Input: ransomNote = "aa", magazine = "ab"
Output: false
```

```text
Input: ransomNote = "aa", magazine = "aab"
Output: true
```

## Solution

```rust
use std::ops::Not;

const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn can_construct(ransom_note: String, magazine: String) -> bool {
    let mut freq = [0i32; LETTERS];
    ransom_note
        .bytes()
        .map(|ch| (ch - b'a') as usize)
        .for_each(|ch| freq[ch] += 1);

    for ch in magazine.bytes().map(|ch| (ch - b'a') as usize) {
        freq[ch] = freq[ch].saturating_sub(1);
    }

    freq.iter().any(|&ch| ch > 0).not()
}
```