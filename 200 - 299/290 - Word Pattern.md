# [290. Word Pattern](https://leetcode.com/problems/word-pattern/)

## Problem

Given a `pattern` and a string `s`, find if `s` follows the same `pattern`.

Here follow means a full match, such that there is a bijection between a letter
in `pattern` and a non-empty word in `s.`.

#### Constraints

* `1 <= pattern.length <= 300`
* `pattern` contains only lower-case English letters.
* `1 <= s.length <= 3000`
* `s` contains only lowercase English letters and spaces `' '`.
* `s` does not contain any leading or trailing spaces.
* All the words in `s` are separated by a single space.

#### Examples

```text
Input: pattern = "abba", s = "dog cat cat dog"
Output: true
```

```text
Input: pattern = "abba", s = "dog cat cat fish"
Output: false
```

```text
Input: pattern = "aaaa", s = "dog cat cat dog"
Output: false
```

## Solution

```rust
use std::collections::HashMap;

const ASCII_AZ: usize = (b'z' - b'a' + 1) as usize;

pub fn word_pattern(pattern: String, s: String) -> bool {
    let words = s.split_ascii_whitespace().collect::<Vec<&str>>();
    if words.len() != pattern.len() {
        // The number of pattern elements does not match the number of words
        return false;
    }

    // mappings between a pattern element and a word
    // because we have only a-z patterns we can use a
    // simple array instead of a HashMap
    let pattern = pattern.as_bytes();
    let mut pattern_to_word = [None; ASCII_AZ];
    let mut word_to_pattern = HashMap::new();

    for (idx, &word) in words.iter().enumerate() {
        let key = (pattern[idx] - b'a') as usize;

        match pattern_to_word[key] {
            None => {
                pattern_to_word[key] = Some(word);
                if word_to_pattern.insert(word, key).is_some() {
                    // The same word is bound to multiple keys, which is not allowed
                    return false;
                }
            }
            Some(value) => {
                if value != word {
                    return false;
                }
            }
        }
    }

    true
}
```