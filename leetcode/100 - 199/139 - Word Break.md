# [139. Word Break](https://leetcode.com/problems/word-break/)

## Problem

### Description

Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s`
can be segmented into a space-separated sequence of one or more dictionary
words.

Note that the same word in the dictionary may be reused multiple times in the
segmentation.

### Constraints

* `1 <= s.length <= 300`
* `1 <= wordDict.length <= 1000`
* `1 <= wordDict[i].length <= 20`
* `s` and `wordDict[i]` consist of only lowercase English letters.
* All the strings of `wordDict` are unique.

### Examples

```text
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```

```text
Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
Note that you are allowed to reuse a dictionary word.
```

```text
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

## Solutions

### Recursion + memoization

```rust
use std::collections::HashSet;

pub fn word_break<S: AsRef<str>>(s: S, word_dict: Vec<String>) -> bool {
    let words = word_dict.into_iter().collect::<HashSet<_>>();
    let mut cache = vec![false; s.as_ref().len() + 1];
    recurse(s.as_ref(), 0, &words, &mut cache)
}

fn recurse(s: &str, from: usize, words: &HashSet<String>, cache: &mut Vec<bool>) -> bool {
    if s[from..].is_empty() {
        return true;
    }

    for to in from..s.len() + 1 {
        if cache[to] {
            continue;
        }

        if words.contains(&s[from..to]) {
            cache[to] = true;

            if recurse(s, to, words, cache) {
                return true;
            }
        }
    }

    false
}
```
