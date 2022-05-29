# [318. Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/)

## Problem

### Description

Given a string array `words`, return the maximum value
of `length(word[i]) * length(word[j])` where the two words do not share common
letters. If no such two words exist, return `0`.

### Constraints

* `2 <= words.length <= 1000`
* `1 <= words[i].length <= 1000`
* `words[i]` consists only of lowercase English letters.

### Examples

```text
Input: words = ["abcw","baz","foo","bar","xtfn","abcdef"]
Output: 16
Explanation: The two words can be "abcw", "xtfn".
```

```text
Input: words = ["a","ab","abc","d","cd","bcd","abcd"]
Output: 4
Explanation: The two words can be "ab", "cd".
```

```text
Input: words = ["a","aa","aaa","aaaa"]
Output: 0
Explanation: No such pair of words.
```

## Solutions

### Bitset + bruteforce (`O(n^2)`)

```rust
pub fn max_product(words: Vec<String>) -> i32 {
    let mut w = words.iter().map(|w| mark_used_chars(w)).collect::<Vec<_>>();

    let mut answer = 0;
    for i in 0..w.len() {
        for j in i + 1..w.len() {
            if w[i] & w[j] == 0 {
                answer = answer.max(words[i].len() * words[j].len());
            }
        }
    }
    answer as i32
}

fn mark_used_chars(w: &str) -> u32 {
    let mut bitset = 0;
    for ch in w.as_bytes().iter().copied() {
        bitset |= 1 << (ch - b'a');
    }
    bitset
}
```

If the input contains a lot of words with the same characters, we can eliminate
the duplicate bitmasks by using a `HashMap`:

```rust
use std::collections::HashMap;

pub fn max_product(words: Vec<String>) -> i32 {
    let mut mask_and_len = HashMap::new();

    words.iter().for_each(|word| {
        let mask = mark_used_chars(word);
        mask_and_len
            .entry(mask)
            .and_modify(|l: &mut usize| *l = (*l).max(word.len()))
            .or_insert(word.len());
    });

    let mask_and_len = mask_and_len.into_iter().collect::<Vec<_>>();

    let mut answer = 0;
    for i in 0..mask_and_len.len() {
        for j in i + 1..mask_and_len.len() {
            if mask_and_len[i].0 & mask_and_len[j].0 == 0 {
                answer = answer.max(mask_and_len[i].1 * mask_and_len[j].1);
            }
        }
    }

    answer as i32
}

fn mark_used_chars(w: &str) -> u32 {
    let mut bitset = 0;
    for ch in w.as_bytes().iter().copied() {
        bitset |= 1 << (ch - b'a');
    }
    bitset
}
```