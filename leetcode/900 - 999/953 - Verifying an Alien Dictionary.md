# [953. Verifying an Alien Dictionary](https://leetcode.com/problems/verifying-an-alien-dictionary/)

## Problem

### Description

In an alien language, surprisingly, they also use English lowercase letters, but
possibly in a different order. The order of the alphabet is some permutation of
lowercase letters.

Given a sequence of words written in the alien language, and the order of the
alphabet, return `true` if and only if the given words are sorted
lexicographically in this alien language.

### Constraints

* `1 <= words.length <= 100`
* `1 <= words[i].length <= 20`
* `order.length == 26`
* All characters in `words[i]` and order are English lowercase letters.

### Examples

```text
Input: words = ["hello","leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz"
Output: true
Explanation: As 'h' comes before 'l' in this language, then the sequence is sorted.
```

```text
Input: words = ["word","world","row"], order = "worldabcefghijkmnpqstuvxyz"
Output: false
Explanation: As 'd' comes after 'l' in this language, then words[0] > words[1], 
hence the sequence is unsorted.
```

```text
Input: words = ["apple","app"], order = "abcdefghijklmnopqrstuvwxyz"
Output: false
Explanation: The first three characters "app" match, and the second string is 
shorter (in size.) According to lexicographical rules "apple" > "app", because 
'l' > '∅', where '∅' is defined as the blank character which is less than any 
other character
```

## Solutions

```rust
use std::cmp::Ordering;

pub fn is_alien_sorted(words: Vec<String>, order: String) -> bool {
    let mut sort_order = vec![0; order.len()];
    for (idx, ch) in order.as_bytes().iter().copied().enumerate() {
        sort_order[(ch - b'a') as usize] = idx as u8;
    }

    words
        .as_slice()
        .windows(2)
        .map(|w| compare(&w[0].as_bytes(), &w[1].as_bytes(), &sort_order))
        .all(|o| o != Ordering::Greater)
}

fn compare(a: &[u8], b: &[u8], d: &[u8]) -> Ordering {
    for idx in 0..a.len().min(b.len()) {
        return match d[(a[idx] - b'a') as usize].cmp(&d[(b[idx] - b'a') as usize]) {
            Ordering::Equal => continue,
            Ordering::Less => Ordering::Less,
            Ordering::Greater => Ordering::Greater,
        };
    }

    a.len().cmp(&b.len())
}
```