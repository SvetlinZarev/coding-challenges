# [208. Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)

## Problem

A [trie] (pronounced as "try") or prefix tree is a tree data structure used to
efficiently store and retrieve keys in a dataset of strings. There are various
applications of this data structure, such as autocomplete and spellchecker.

Implement the Trie class:

* `Trie()` Initializes the trie object.
* `void insert(String word)` Inserts the string word into the trie.
* `boolean search(String word)` Returns true if the string word is in the trie (
  i.e., was inserted before), and false otherwise.
* `boolean startsWith(String prefix)` Returns true if there is a previously
  inserted string word that has the prefix `prefix`, and false otherwise.

#### Constraints:

* `1 <= word.length, prefix.length <= 2000`
* `word` and `prefix` consist only of lowercase English letters.
* At most `3 * 104` calls in total will be made to `insert`, `search`,
  and `startsWith`.

## Solutions

### Using an array for the trie nodes

```rust
#[derive(Default, Debug)]
struct Node {
    ptr: [Option<Box<Node>>; 26],
    end: bool,
}

impl Node {
    pub fn new() -> Self {
        Default::default()
    }

    pub fn search(&self, word: &[u8]) -> bool {
        self.find(word).map(|n| n.end).unwrap_or(false)
    }

    pub fn starts_with(&self, prefix: &[u8]) -> bool {
        self.find(prefix).is_some()
    }

    fn find(&self, word: &[u8]) -> Option<&Node> {
        let mut node = self;

        for ch in word.iter().copied().map(|ch| (ch - b'a') as usize) {
            // if ch >= self.ptr.len() {
            //     return None;
            // }

            match &node.ptr[ch] {
                Some(link) => node = link,
                None => return None,
            }
        }

        Some(node)
    }

    pub fn insert(&mut self, word: &[u8]) {
        // if let Some(ch) = word.iter().copied().map(|ch| ch - b'a').find(|ch| *ch > 25) {
        //     panic!("Found non a-z character!: {}", ch + b'a');
        // }

        let mut node = self;
        for ch in word.iter().copied().map(|ch| (ch - b'a') as usize) {
            node = node.ptr[ch].get_or_insert_with(|| Box::new(Node::new()))
        }
        node.end = true;
    }
}

#[derive(Debug, Default)]
struct Trie {
    inner: Node,
}

impl Trie {
    fn new() -> Self {
        Default::default()
    }

    fn insert(&mut self, word: String) {
        self.inner.insert(word.as_bytes());
    }

    fn search(&self, word: String) -> bool {
        self.inner.search(word.as_bytes())
    }

    fn starts_with(&self, prefix: String) -> bool {
        self.inner.starts_with(prefix.as_bytes())
    }
}
```

### Using a `HashMap` for the trie nodes

```rust
use std::collections::HashMap;

#[derive(Default, Debug)]
struct Node {
    ptr: HashMap<u8, Node>,
    end: bool,
}

impl Node {
    pub fn new() -> Self {
        Default::default()
    }

    pub fn search(&self, word: &[u8]) -> bool {
        self.find(word).map(|n| n.end).unwrap_or(false)
    }

    pub fn starts_with(&self, prefix: &[u8]) -> bool {
        self.find(prefix).is_some()
    }

    fn find(&self, word: &[u8]) -> Option<&Node> {
        let mut node = self;

        for ch in word.iter() {
            match node.ptr.get(ch) {
                Some(link) => node = link,
                None => return None,
            }
        }

        Some(node)
    }

    pub fn insert(&mut self, word: &[u8]) {
        let mut node = self;
        for ch in word.iter().copied() {
            node = node.ptr.entry(ch).or_default()
        }
        node.end = true;
    }
}

#[derive(Debug, Default)]
struct Trie {
    inner: Node,
}

impl Trie {
    fn new() -> Self {
        Default::default()
    }

    fn insert(&mut self, word: String) {
        self.inner.insert(word.as_bytes());
    }

    fn search(&self, word: String) -> bool {
        self.inner.search(word.as_bytes())
    }

    fn starts_with(&self, prefix: String) -> bool {
        self.inner.starts_with(prefix.as_bytes())
    }
}
```

[trie]: https://en.wikipedia.org/wiki/Trie