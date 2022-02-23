# [720. Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary/)

## Problem

Given an array of strings words representing an English Dictionary, return the
longest word in words that can be built one character at a time by other words
in words.

If there is more than one possible answer, return the longest word with the
smallest lexicographical order. If there is no answer, return the empty string.

#### Constraints

* `1 <= words.length <= 1000`
* `1 <= words[i].length <= 30`
* `words[i]` consists of lowercase English letters.

#### Examples

```text
Input: words = ["a","banana","app","appl","ap","apply","apple"]
Output: "apple"
Explanation: Both "apply" and "apple" can be built from other words in the dictionary. However, "apple" is lexicographically smaller than "apply".
```

```text
Input: words = ["w","wo","wor","worl","world"]
Output: "world"
Explanation: The word "world" can be built one character at a time by "w", "wo", "wor", and "worl".
```

## Solution

### DFS + Trie

```rust
use std::collections::HashMap;

#[derive(Default)]
struct Node {
    children: HashMap<char, Node>,
    word: Option<String>,
}

impl Node {
    fn insert(&mut self, word: String) {
        let mut node = self;
        for ch in word.chars() {
            node = node.children.entry(ch).or_default();
        }
        node.word = Some(word);
    }

    fn longest(&self) -> String {
        fn dfs(node: &Node, best: &mut String) {
            for child in node
                .children
                .iter()
                .map(|(_, ch)| ch)
                .filter(|c| c.word.is_some())
            {
                let x = child.word.as_ref().unwrap();
                if best.len() < x.len() || (best.len() == x.len() && x < best) {
                    best.clear();
                    best.extend(x.chars());
                }

                dfs(child, best);
            }
        }

        let mut best = String::new();
        dfs(self, &mut best);
        best
    }
}

pub fn longest_word(words: Vec<String>) -> String {
    let mut trie = Node::default();
    for word in words {
        trie.insert(word);
    }

    trie.longest()
}
```