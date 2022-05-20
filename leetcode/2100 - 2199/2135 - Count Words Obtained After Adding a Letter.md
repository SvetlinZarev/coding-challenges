# [2135. Count Words Obtained After Adding a Letter](https://leetcode.com/problems/count-words-obtained-after-adding-a-letter/)

## Problem

### Description

You are given two 0-indexed arrays of strings `startWords` and `targetWords`.
Each string consists of lowercase English letters only.

For each string in `targetWords`, check if it is possible to choose a string
from `startWords` and perform a conversion operation on it to be equal to that
from `targetWords`.

The conversion operation is described in the following two steps:

* Append any lowercase letter that is not present in the string to its end.
    * For example, if the string is `abc`, the letters `d`, `e`, or `y` can be
      added to it, but not `a`. If `d` is added, the resulting string will
      be `abcd`.
* Rearrange the letters of the new string in any arbitrary order.
    * For example, `abcd` can be rearranged to `acbd`, `bacd`, `cbda`, and so
      on. Note that it can also be rearranged to `abcd` itself.

Return the number of strings in `targetWords` that can be obtained by performing
the operations on any string of `startWords`.

Note that you will only be verifying if the string in `targetWords` can be
obtained from a string in `startWords` by performing the operations. The strings
in `startWords` do not actually change during this process.

### Constraints

* `1 <= startWords.length, targetWords.length <= 5 * 10^4`
* `1 <= startWords[i].length, targetWords[j].length <= 26`
* Each string of `startWords` and `targetWords` consists of lowercase English
  letters only.
* No letter occurs more than once in any string of `startWords` or `targetWords`

## Solutions

### Tracking character frequencies

Time @ OJ: 130 at the time of writing

```rust
use std::collections::HashSet;

pub fn word_count(start_words: Vec<String>, target_words: Vec<String>) -> i32 {
    let mut start_freq = HashSet::new();
    start_words.into_iter().for_each(|w| {
        start_freq.insert(freq(&w));
    });

    let mut answer = 0;
    target_words.into_iter().for_each(|w| {
        let mut f = freq(&w);

        // remove one character at a time
        for &ch in w.as_bytes() {
            let idx = (ch - b'a') as usize;

            f[idx] = 0;
            if start_freq.contains(&f) {
                answer += 1;
                break;
            }
            f[idx] = 1;
        }
    });

    answer
}

fn freq(word: &str) -> [u8; (b'z' - b'a' + 1) as usize] {
    let mut f = [0u8; (b'z' - b'a' + 1) as usize];

    for &ch in word.as_bytes() {
        f[(ch - b'a') as usize] += 1;
    }

    f
}
```

### Using bitflags

The same idea as above, but takes advantage of the fact that each character can
appear at most once in a string.

Time @ OJ: 90ms at the time of writing

```rust
use std::collections::HashSet;

pub fn word_count(start_words: Vec<String>, target_words: Vec<String>) -> i32 {
    let mut start_freq = HashSet::new();
    start_words.into_iter().for_each(|w| {
        start_freq.insert(freq(&w));
    });

    let mut answer = 0;
    target_words.into_iter().for_each(|w| {
        let f = freq(&w);

        for &ch in w.as_bytes() {
            let x = f ^ (1 << ((ch - b'a') as u32));
            if start_freq.contains(&x) {
                answer += 1;
                break;
            }
        }
    });

    answer
}

fn freq(word: &str) -> u32 {
    let mut f = 0;

    for &ch in word.as_bytes() {
        f |= 1 << ch - b'a';
    }

    f
}
```

### Using a trie

Time @ OJ: 220-300ms at the time of writing

```rust
use std::collections::HashMap;

#[derive(Default)]
struct Node {
    next: HashMap<char, Node>,
    is_word: bool,
}

impl Node {
    pub fn insert(&mut self, word: &[char]) {
        let mut node = self;

        for &ch in word {
            node = node.next.entry(ch).or_default();
        }

        node.is_word = true;
    }

    pub fn find(&self, word: &[char], skip_idx: usize) -> bool {
        let mut node = self;
        for (idx, ch) in word.iter().enumerate() {
            if idx == skip_idx {
                continue;
            }

            node = match node.next.get(ch) {
                None => return false,
                Some(node) => node,
            };
        }

        node.is_word
    }
}

pub fn word_count(start_words: Vec<String>, target_words: Vec<String>) -> i32 {
    let mut trie = Node::default();
    for word in start_words {
        let mut chars = word.chars().collect::<Vec<_>>();
        chars.sort_unstable();
        trie.insert(&chars)
    }

    let mut answer = 0;
    'next: for word in target_words {
        let mut chars = word.chars().collect::<Vec<_>>();
        chars.sort_unstable();

        // skip one character at a time and try to find a match
        for idx in 0..chars.len() {
            if trie.find(&chars, idx) {
                answer += 1;
                continue 'next;
            }
        }
    }

    answer
}
```

Or instead of skipping a character in the loop and doing N searches, we can skip
it while doing DFS:

Time @ OJ: 220-300ms at the time of writing

```rust
use std::collections::HashMap;

#[derive(Default)]
struct Node {
    next: HashMap<char, Node>,
    is_word: bool,
}

impl Node {
    pub fn insert(&mut self, word: &[char]) {
        let mut node = self;

        for &ch in word {
            node = node.next.entry(ch).or_default();
        }

        node.is_word = true;
    }

    pub fn find(&self, word: &[char]) -> bool {
        let mut node = self;
        for ch in word.iter() {
            node = match node.next.get(ch) {
                None => return false,
                Some(node) => node,
            };
        }

        node.is_word
    }

    pub fn skip_find(&self, word: &[char], idx: usize, skipped: bool) -> bool {
        if !skipped {
            // Because we have to skip only one character, switch to the iterative
            // version after we skip it because it's more efficient
            if self.find(&word[idx + 1..]) {
                return true;
            }
        }

        if idx < word.len() - 1 {
            if let Some(next) = self.next.get(&word[idx]) {
                if next.skip_find(word, idx + 1, false) {
                    return true;
                }
            }
        }

        false
    }
}

pub fn word_count(start_words: Vec<String>, target_words: Vec<String>) -> i32 {
    let mut trie = Node::default();
    for word in start_words {
        let mut chars = word.chars().collect::<Vec<_>>();
        chars.sort_unstable();
        trie.insert(&chars)
    }

    let mut answer = 0;
    for word in target_words {
        let mut chars = word.chars().collect::<Vec<_>>();
        chars.sort_unstable();

        if trie.skip_find(&chars, 0, false) {
            answer += 1;
        }
    }

    answer
}
```
