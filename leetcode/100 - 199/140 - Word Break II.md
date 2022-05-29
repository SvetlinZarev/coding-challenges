# [140. Word Break II](https://leetcode.com/problems/word-break-ii/)

## Problem

### Description

Given a string `s` and a dictionary of strings `wordDict`, add spaces in `s` to
construct a sentence where each word is a valid dictionary word. Return all such
possible sentences in any order.

Note that the same word in the dictionary may be reused multiple times in the
segmentation.

### Constraints

* `1 <= s.length <= 20`
* `1 <= wordDict.length <= 1000`
* `1 <= wordDict[i].length <= 10`
* `s` and `wordDict[i]` consist of only lowercase English letters.
* All the strings of `wordDict` are unique.

### Examples

```text
Input: s = "catsanddog", wordDict = ["cat","cats","and","sand","dog"]
Output: ["cats and dog","cat sand dog"]
```

```text
Input: s = "pineapplepenapple", wordDict = ["apple","pen","applepen","pine","pineapple"]
Output: ["pine apple pen apple","pineapple pen apple","pine applepen apple"]
Explanation: Note that you are allowed to reuse a dictionary word.
```

```text
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: []
```

## Solutions

### Backtracking

```rust
use std::collections::HashSet;

pub fn word_break<S: AsRef<str>>(s: S, word_dict: Vec<String>) -> Vec<String> {
    let words = word_dict.into_iter().collect::<HashSet<_>>();
    let mut answer = vec![];
    let mut buffer = vec![];

    backtrack(s.as_ref(), 0, &words, &mut answer, &mut buffer);

    answer
}

fn backtrack<'l>(
    s: &'l str,
    from: usize,
    words: &HashSet<String>,
    answer: &mut Vec<String>,
    buffer: &mut Vec<&'l str>,
) {
    if s[from..].is_empty() {
        let sentence = buffer.join(" ");
        answer.push(sentence);
        return;
    }

    for to in from + 1..s.len() + 1 {
        if words.contains(&s[from..to]) {
            buffer.push(&s[from..to]);
            backtrack(s, to, words, answer, buffer);
            buffer.pop();
        }
    }
}
```

We can also add memoization to skip starting positions which are known to not
lead to an answer:

```rust
use std::collections::HashSet;

pub fn word_break<S: AsRef<str>>(s: S, word_dict: Vec<String>) -> Vec<String> {
    let words = word_dict.into_iter().collect::<HashSet<_>>();
    let mut answer = vec![];
    let mut buffer = vec![];
    let mut cache = vec![true; s.as_ref().len() + 1];

    backtrack(s.as_ref(), 0, &words, &mut answer, &mut buffer, &mut cache);

    answer
}

fn backtrack<'l>(
    s: &'l str,
    from: usize,
    words: &HashSet<String>,
    answer: &mut Vec<String>,
    buffer: &mut Vec<&'l str>,
    cache: &mut Vec<bool>,
) -> bool {
    if s[from..].is_empty() {
        let sentence = buffer.join(" ");
        answer.push(sentence);
        return true;
    }

    if !cache[from] {
        return false;
    }

    let mut with_answer = false;
    for to in from + 1..s.len() + 1 {
        if words.contains(&s[from..to]) {
            buffer.push(&s[from..to]);
            with_answer |= backtrack(s, to, words, answer, buffer, cache);
            buffer.pop();
        }
    }
    cache[from] = with_answer;

    with_answer
}
```

## Related problems

* [139. Word Break](139%20-%20Word%20Break.md)
