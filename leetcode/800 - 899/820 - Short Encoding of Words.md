# [820. Short Encoding of Words](https://leetcode.com/problems/short-encoding-of-words/)

## Problem

### Description

A valid encoding of an array of words is any reference string `s` and array of
indices `indices` such that:

* `words.length == indices.length`
* The reference string `s` ends with the `#` character.
* For each index `indices[i]`, the substring of `s` starting from `indices[i]`
  and up to (but not including) the next `#` character is equal to `words[i]`.

Given an array of words, return the length of the shortest reference string `s`
possible of any valid encoding of words.

### Constraints

* `1 <= words.length <= 2000`
* `1 <= words[i].length <= 7`
* `words[i]` consists of only lowercase letters.

### Examples

#### Example 1

```text
Input: words = ["time", "me", "bell"]
Output: 10
```

> Explanation: A valid encoding would be `s = "time#bell#"` and indices
> = `[0, 2, 5]`.
>
> `words[0] = "time"`, the substring of `s` starting from `indices[0] = 0` to
> the next `#` is underlined in `>time<#bell#`
>
> `words[1] = "me"`, the substring of `s` starting from `indices[1] = 2` to the
> next `#` is underlined in `ti>me<#bell#`
>
> `words[2] = "bell"`, the substring of `s` starting from `indices[2] = 5` to
> the next `#` is underlined in `time#>bell<#`

#### Example 2

```text
Input: words = ["t"]
Output: 2
```

> Explanation: A valid encoding would be `s = "t#"` and `indices = [0]`.

## Solutions

### Sorting

```rust
pub fn minimum_length_encoding(mut words: Vec<String>) -> i32 {
    if words.is_empty() {
        return 0;
    }

    // SAFETY: Because the words consist only of lowercase ASCII it's safe to 
    // reverse them byte-by-byte
    words
        .iter_mut()
        .for_each(|w| unsafe { w.as_bytes_mut().reverse() });

    // Sort the words by prefix and length, thus a word at `idx-1` can be 
    // considered as a prefix of word at `idx`, if the second word starts 
    // with the first one
    words.sort_unstable();

    let mut answer = 0;
    let mut longest_word = words[0].len();

    for idx in 1..words.len() {
        // If the word at `idx -1` is not a prefix of the word at `idx`, we 
        // must add a new word to our reference string, thus the length of the
        // reference string increases
        if !(words[idx].starts_with(&words[idx - 1])) {
            // Plus 1 because of the `#` terminator
            answer += longest_word + 1;
        }

        longest_word = words[idx].len();
    }

    // Handle the last word - it needs to be added to the reference string
    answer += longest_word + 1;

    answer as i32
}
```

### Trie

Finding the common suffix is well suited for a Trie, where we insert the
characters of the words in reversed order.

The key observation here is that the length of the "reference string" is the sum
of the depths of the leaf nodes plus the number of leaf nodes:

```text
Input: ["time", "atime", "btime"]

          e      1
          |
          m      2
          |
          i      3
          |
          t      4
         / \
        a   b    5
        
```

The reference string is `atime#btime#` with length of 12 -> two nodes with
depth of 5, plus the number of nodes: `2 * 5 + 2 = 12`

```rust
use std::collections::HashMap;

#[derive(Default)]
struct Node {
    children: HashMap<u8, Node>,
}

impl Node {
    fn insert(&mut self, word: impl Iterator<Item=u8>) {
        let mut node = self;
        for ch in word {
            node = node.children.entry(ch).or_default();
        }
    }

    fn visit<T>(&self, action: impl Fn(&Node) -> T) -> T {
        action(self)
    }
}

fn sum_leaf_node_depths(node: &Node, depth: i32) -> i32 {
    if node.children.is_empty() {
        // Plus one, because we need to account for the number of nodes
        return depth + 1;
    }

    let mut sum = 0;
    for child in node.children.values() {
        sum += sum_leaf_node_depths(child, depth + 1);
    }
    sum
}

pub fn minimum_length_encoding(words: Vec<String>) -> i32 {
    if words.is_empty() {
        return 0;
    }

    let mut trie = Node::default();
    words.iter().for_each(|w| trie.insert(w.bytes().rev()));

    trie.visit(|x| sum_leaf_node_depths(x, 0))
}
```