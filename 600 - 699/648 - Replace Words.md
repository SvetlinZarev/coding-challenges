# [648. Replace Words](https://leetcode.com/problems/replace-words/)

## Problem

In English, we have a concept called root, which can be followed by some other
word to form another longer word - let's call this word successor. For example,
when the root "an" is followed by the successor word "other", we can form a new
word "another".

Given a `dictionary` consisting of many roots and a `sentence` consisting of
words separated by spaces, replace all the successors in the sentence with the
root forming it. If a successor can be replaced by more than one root, replace
it with the root that has the shortest length.

Return the sentence after the replacement.

#### Constraints

* `1 <= dictionary.length <= 1000`
* `1 <= dictionary[i].length <= 100`
* `dictionary[i]` consists of only lower-case letters.
* `1 <= sentence.length <= 10^6`
* `sentence` consists of only lower-case letters and spaces.
* The number of words in `sentence` is in the range `[1, 1000]`
* The length of each word in `sentence` is in the range `[1, 1000]`
* Every two consecutive words in `sentence` will be separated by exactly one
  space.
* `sentence` does not have leading or trailing spaces.

#### Examples

```text
Input: dictionary = ["cat","bat","rat"], sentence = "the cattle was rattled by the battery"
Output: "the cat was rat by the bat"
```

```text
Input: dictionary = ["a","b","c"], sentence = "aadsfasf absbs bbab cadsfafs"
Output: "a a b c"
```

## Solutions

### Using a Trie (recursive implementation)

```rust
use std::collections::HashMap;

#[derive(Debug, Default)]
struct Node<'l> {
    values: HashMap<u8, Node<'l>>,
    root: Option<&'l str>,
}

impl<'l> Node<'l> {
    pub fn insert(&mut self, word: &'l str) {
        insert(self, word.as_bytes(), word);
    }

    pub fn find_root(&self, word: &str) -> Option<&str> {
        find_root(self, word.as_bytes())
    }
}

fn insert<'n, 'w: 'n>(node: &'n mut Node<'w>, word: &'w [u8], root: &'w str) {
    if word.is_empty() {
        node.root = Some(root);
        return;
    }

    let next = node.values.entry(word[0]).or_default();
    insert(next, &word[1..], root);
}

fn find_root<'n>(node: &'n Node<'n>, word: &[u8]) -> Option<&'n str> {
    if node.root.is_some() || word.is_empty() {
        return node.root.clone();
    }

    let ch = word[0];
    match node.values.get(&ch) {
        None => None,
        Some(next) => find_root(next, &word[1..]),
    }
}

pub fn replace_words(dictionary: Vec<String>, sentence: String) -> String {
    let mut trie = Node::default();
    dictionary.iter().for_each(|w| trie.insert(w));

    let mut result = String::new();
    for word in sentence.split_ascii_whitespace() {
        if !result.is_empty() {
            result.push_str(" ");
        }
        result.push_str(trie.find_root(word).unwrap_or(word));
    }

    result
}
```

### using a Trie (iterative implementation)

```rust
use std::collections::hash_map::Entry;
use std::collections::HashMap;

#[derive(Debug, Default)]
struct Node<'l> {
    values: HashMap<u8, Node<'l>>,
    root: Option<&'l str>,
}

impl<'l> Node<'l> {
    pub fn insert(&mut self, word: &'l str) {
        let mut node = self;
        let mut remainng = word.as_bytes();

        while !remainng.is_empty() {
            let ch = remainng[0];
            remainng = &remainng[1..];

            match node.values.entry(ch) {
                Entry::Occupied(e) => node = e.into_mut(),
                Entry::Vacant(e) => node = e.insert(Node::default()),
            }
        }

        node.root = Some(word);
    }

    pub fn find_root(&self, word: &str) -> Option<&str> {
        let mut current = self;
        let mut word = word.as_bytes();

        while current.root.is_none() && !word.is_empty() {
            let ch = word[0];

            word = &word[1..];
            current = current.values.get(&ch)?;
        }

        current.root.clone()
    }
}

pub fn replace_words(dictionary: Vec<String>, sentence: String) -> String {
    let mut trie = Node::default();
    dictionary.iter().for_each(|w| trie.insert(w));

    let mut result = String::new();
    for word in sentence.split_ascii_whitespace() {
        if !result.is_empty() {
            result.push_str(" ");
        }
        result.push_str(trie.find_root(word).unwrap_or(word));
    }

    result
}
```

## Related Problems

* [208. Implement Trie (Prefix Tree)](/200%20-%20299/208%20-%20Implement%20Trie%20(Prefix%20Tree).md)