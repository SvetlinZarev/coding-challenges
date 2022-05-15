# [1160. Find Words That Can Be Formed by Characters](https://leetcode.com/problems/find-words-that-can-be-formed-by-characters/)

## Problem

### Description

You are given an array of strings `words` and a string `chars`.

A string is good if it can be formed by characters from `chars` (each character
can only be used once).

Return the sum of lengths of all good strings in `words`.

### Constraints

* `1 <= words.length <= 1000`
* `1 <= words[i].length, chars.length <= 100`
* `words[i]` and chars consist of lowercase English letters.

### Examples

```text
Input: words = ["cat","bt","hat","tree"], chars = "atach"
Output: 6
Explanation: The strings that can be formed are "cat" and "hat" so the answer is 3 + 3 = 6.
```

```text
Input: words = ["hello","world","leetcode"], chars = "welldonehoneyr"
Output: 10
Explanation: The strings that can be formed are "hello" and "world" so the answer is 5 + 5 = 10.
```

## Solutions

### Tracking character counts

```rust
pub fn count_characters(words: Vec<String>, chars: String) -> i32 {
    let mut freq = [0; (b'z' - b'a' + 1) as usize];
    chars
        .as_bytes()
        .iter()
        .copied()
        .map(|ch| (ch - b'a') as usize)
        .for_each(|ch| {
            freq[ch] += 1;
        });

    let mut answer = 0;

    'next: for word in words {
        let mut freq = freq.clone();

        for ch in word
            .as_bytes()
            .iter()
            .copied()
            .map(|ch| (ch - b'a') as usize)
        {
            freq[ch] -= 1;
            if freq[ch] < 0 {
                // if there is no enough amount of the required character,
                // then this word cannot be formed from the provided characters,
                // thus we skip it and go to the next word
                continue 'next;
            }
        }

        answer += word.len() as i32;
    }

    answer
}
```
