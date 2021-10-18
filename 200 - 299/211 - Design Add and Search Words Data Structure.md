# [211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

## Problem

Design a data structure that supports adding new words and finding if a string
matches any previously added string.

Implement the `WordDictionary` class:

* `WordDictionary()` Initializes the object.
* `void addWord(word)` Adds word to the data structure, it can be matched later.
* `bool search(word)` Returns `true` if there is any string in the data
  structure that matches word or `false` otherwise. Word may contain dots `.`
  where dots can be matched with any letter.

#### Constraints

* `1 <= word.length <= 500`
* `word` in `addWord` consists lower-case English letters.
* `word` in `search` consist of  `.` or lower-case English letters.
* At most 50000 calls will be made to `addWord` and `search`.

#### Examples

```text
Input
["WordDictionary","addWord","addWord","addWord","search","search","search","search"]
[[],["bad"],["dad"],["mad"],["pad"],["bad"],[".ad"],["b.."]]
Output
[null,null,null,null,false,true,true,true]

Explanation
WordDictionary wordDictionary = new WordDictionary();
wordDictionary.addWord("bad");
wordDictionary.addWord("dad");
wordDictionary.addWord("mad");
wordDictionary.search("pad"); // return False
wordDictionary.search("bad"); // return True
wordDictionary.search(".ad"); // return True
wordDictionary.search("b.."); // return True
```

## Solution

```rust
use std::collections::HashMap;

#[derive(Default)]
struct WordDictionary {
    children: Node,
}

// The API is defined by LeetCode
impl WordDictionary {
    fn new() -> Self {
        Default::default()
    }

    fn add_word(&mut self, word: String) {
        self.children.insert(word.chars());
    }

    fn search(&self, word: String) -> bool {
        self.children.find(word.chars())
    }
}

#[derive(Default)]
struct Node {
    children: HashMap<char, Node>,
    is_word: bool,
}

impl Node {
    fn insert(&mut self, word: impl Iterator<Item=char>) {
        let mut node = self;
        for ch in word {
            node = node.children.entry(ch).or_default();
        }
        node.is_word = true;
    }

    fn find(&self, mut word: impl Iterator<Item=char> + Clone) -> bool {
        let mut node = self;

        while let Some(ch) = word.next() {
            if ch == '.' {
                for (_, n) in node.children.iter() {
                    if n.find(word.clone()) {
                        return true;
                    }
                }

                return false;
            }

            match node.children.get(&ch) {
                None => return false,
                Some(n) => node = n,
            }
        }

        node.is_word
    }
}
```