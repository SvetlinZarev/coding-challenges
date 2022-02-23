# [745. Prefix and Suffix Search](https://leetcode.com/problems/prefix-and-suffix-search/)

## Problem

Design a special dictionary with some words that searches the words in it by
a `prefix` and a `suffix`.

Implement the `WordFilter` class:

* `WordFilter(string[] words)` Initializes the object with the words in the
  dictionary.
* `f(string prefix, string suffix)` Returns the index of the word in the
  dictionary, which has the `prefix` prefix and the `suffix` suffix. If there is
  more than one valid index, return the largest of them. If there is no such
  word in the dictionary, return `-1`.

#### Constraints

* `1 <= words.length <= 15000`
* `1 <= words[i].length <= 10`
* `1 <= prefix.length, suffix.length <= 10`
* `words[i]`, `prefix` and `suffix` consist of lower-case English letters only.
* At most 15000 calls will be made to the function `f`.

#### Examples

```text
Input
["WordFilter", "f"]
[[["apple"]], ["a", "e"]]
Output
[null, 0]
```

```text
Explanation
WordFilter wordFilter = new WordFilter(["apple"]);
wordFilter.f("a", "e"); // return 0, because the word at index 0 has prefix = "a" and suffix = 'e".
```

## Solutions

### Trie + linear search

```rust
use std::collections::HashMap;
use std::num::NonZeroUsize;

const NO_SUCH_WORD: i32 = -1;

#[derive(Default)]
struct Node {
    values: HashMap<u8, Node>,
    index: Option<NonZeroUsize>,
}

impl Node {
    pub fn insert(&mut self, word: &str, index: Option<usize>) -> &mut Node {
        let mut node = self;
        for &ch in word.as_bytes() {
            node = node.values.entry(ch).or_default();
        }

        if let Some(index) = index {
            node.index = NonZeroUsize::new(index + 1);
        }

        node
    }

    pub fn find(&self, term: &str) -> Option<&Node> {
        let mut node = self;
        for ch in term.as_bytes() {
            node = node.values.get(ch)?;
        }
        Some(node)
    }

    pub fn collect(&self) -> Vec<usize> {
        let mut result = vec![];
        collect(self, &mut result);
        result
    }
}

fn collect(node: &Node, dst: &mut Vec<usize>) {
    if let Some(idx) = node.index.clone() {
        dst.push(idx.get() - 1);
    }

    for next in node.values.values() {
        collect(next, dst);
    }
}

struct WordFilter {
    trie: Node,
    words: Vec<String>,
}

impl WordFilter {
    fn new(words: Vec<String>) -> Self {
        let mut trie = Node::default();

        for (idx, word) in words.iter().enumerate() {
            trie.insert(&word, Some(idx));
        }

        Self { trie, words }
    }

    fn f(&self, prefix: String, suffix: String) -> i32 {
        if let Some(node) = self.trie.find(&prefix) {
            let mut words = node.collect();
            words.sort_unstable();

            for word_idx in words.iter().copied().rev() {
                if self.words[word_idx].ends_with(&suffix) {
                    return word_idx as i32;
                }
            }
        }

        NO_SUCH_WORD
    }
}
```

### Two tries + set intersection

```rust
use std::collections::{HashMap, HashSet};
use std::num::NonZeroUsize;

const NO_SUCH_WORD: i32 = -1;

#[derive(Default)]
struct Node {
    values: HashMap<u8, Node>,
    index: Option<NonZeroUsize>,
}

impl Node {
    pub fn insert<I: Iterator<Item=u8>>(&mut self, word: I, index: usize) {
        let mut node = self;
        for ch in word {
            node = node.values.entry(ch).or_default();
        }

        node.index = NonZeroUsize::new(index + 1);
    }

    pub fn collect<I: Iterator<Item=u8>>(&self, term: I) -> Vec<usize> {
        let mut node = self;
        for ch in term {
            match node.values.get(&ch) {
                None => return vec![],
                Some(next) => node = next,
            }
        }

        let mut result = vec![];
        collect(node, &mut result);
        result
    }
}

fn collect(node: &Node, dst: &mut Vec<usize>) {
    if let Some(idx) = node.index.clone() {
        dst.push(idx.get() - 1);
    }

    for next in node.values.values() {
        collect(next, dst);
    }
}

struct WordFilter {
    prefix: Node,
    suffix: Node,
}

impl WordFilter {
    fn new(words: Vec<String>) -> Self {
        let mut prefix = Node::default();
        words
            .iter()
            .enumerate()
            .for_each(|(idx, w)| prefix.insert(w.as_bytes().iter().copied(), idx));

        let mut suffix = Node::default();
        words
            .iter()
            .enumerate()
            .for_each(|(idx, w)| suffix.insert(w.as_bytes().iter().copied().rev(), idx));

        Self { prefix, suffix }
    }

    fn f(&self, prefix: String, suffix: String) -> i32 {
        let mut p = self.prefix.collect(prefix.as_bytes().iter().copied());
        if p.is_empty() {
            return NO_SUCH_WORD;
        }

        let mut s = self.suffix.collect(suffix.as_bytes().iter().copied().rev());
        if s.is_empty() {
            return NO_SUCH_WORD;
        }

        let (smaller, larger) = if p.len() > s.len() {
            (&mut s, &mut p)
        } else {
            (&mut p, &mut s)
        };

        let set = smaller.iter().copied().collect::<HashSet<usize>>();
        larger.retain(|x| set.contains(x));

        if larger.is_empty() {
            return NO_SUCH_WORD;
        }

        larger
            .iter()
            .copied()
            .map(|x| x as i32)
            .max()
            .unwrap_or(NO_SUCH_WORD)
    }
}
```

### Trie of suffix-wrapped words

```rust
use std::collections::HashMap;
use std::num::NonZeroUsize;

const NO_SUCH_WORD: i32 = -1;

#[derive(Default)]
struct Node {
    values: HashMap<u8, Node>,
    index: Option<NonZeroUsize>,
}

impl Node {
    pub fn insert(&mut self, word: &str, index: Option<usize>) -> &mut Node {
        let mut node = self;
        for &ch in word.as_bytes() {
            node = node.values.entry(ch).or_default();
        }

        if let Some(index) = index {
            node.index = NonZeroUsize::new(index + 1);
        }

        node
    }

    pub fn find(&self, term: &str) -> Option<&Node> {
        let mut node = self;
        for ch in term.as_bytes() {
            node = node.values.get(ch)?;
        }
        Some(node)
    }

    pub fn collect(&self) -> Vec<usize> {
        let mut result = vec![];
        collect(self, &mut result);
        result
    }
}

fn collect(node: &Node, dst: &mut Vec<usize>) {
    if let Some(idx) = node.index.clone() {
        dst.push(idx.get() - 1);
    }

    for next in node.values.values() {
        collect(next, dst);
    }
}

struct WordFilter {
    trie: Node,
}

impl WordFilter {
    fn new(words: Vec<String>) -> Self {
        let mut trie = Node::default();

        for (idx, word) in words.into_iter().enumerate() {
            for sfx_len in 1..=word.len() {
                let suffix = &word[word.len() - sfx_len..];
                let mut next = trie.insert(suffix, None);
                next = next.insert("#", None);
                next.insert(&word, Some(idx));
            }
        }

        Self { trie }
    }

    fn f(&self, prefix: String, suffix: String) -> i32 {
        if let Some(node) = self.trie.find(&suffix) {
            if let Some(node) = node.find("#") {
                if let Some(node) = node.find(&prefix) {
                    return node
                        .collect()
                        .iter()
                        .copied()
                        .map(|x| x as i32)
                        .max()
                        .unwrap_or(NO_SUCH_WORD);
                }
            }
        }

        NO_SUCH_WORD
    }
}
```

## Related Problems

* [211. Design Add and Search Words Data Structure](/leetcode/200%20-%20299/211%20-%20Design%20Add%20and%20Search%20Words%20Data%20Structure.md)