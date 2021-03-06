# [1268. Search Suggestions System](https://leetcode.com/problems/search-suggestions-system/)

## Problem

### Description

You are given an array of strings `products` and a string `searchWord`.

Design a system that suggests at most three product names from `products` after
each character of `searchWord` is typed. Suggested products should have common
prefix with `searchWord`. If there are more than three products with a common
prefix return the three lexicographically minimums products.

Return a list of lists of the suggested products after each character
of `searchWord` is typed.

### Constraints

* `1 <= products.length <= 1000`
* `1 <= products[i].length <= 3000`
* `1 <= sum(products[i].length) <= 2 * 10^4`
* All the strings of `products` are unique.
* `products[i]` consists of lowercase English letters.
* `1 <= searchWord.length <= 1000`
* `searchWord` consists of lowercase English letters.

### Examples

```text
Input: products = ["mobile","mouse","moneypot","monitor","mousepad"], searchWord = "mouse"
Output: [
["mobile","moneypot","monitor"],
["mobile","moneypot","monitor"],
["mouse","mousepad"],
["mouse","mousepad"],
["mouse","mousepad"]
]
Explanation: products sorted lexicographically = ["mobile","moneypot","monitor","mouse","mousepad"]
After typing m and mo all products match and we show user ["mobile","moneypot","monitor"]
After typing mou, mous and mouse the system suggests ["mouse","mousepad"]
```

```text
Input: products = ["havana"], searchWord = "havana"
Output: [["havana"],["havana"],["havana"],["havana"],["havana"],["havana"]]
```

```text
Input: products = ["bags","baggage","banner","box","cloths"], searchWord = "bags"
Output: [["baggage","bags","banner"],["baggage","bags","banner"],["baggage","bags"],["bags"]]
```

## Solutions

### Naive solution

```rust
pub fn suggested_products(mut products: Vec<String>, search_word: String) -> Vec<Vec<String>> {
    products.sort_unstable();

    let mut suggestions = Vec::with_capacity(search_word.len());
    for prefix_len in 1..=search_word.len() {
        let suggestion = products
            .iter()
            .filter(|&w| w.starts_with(&search_word[..prefix_len]))
            .take(3)
            .map(|w| w.clone())
            .collect();
        suggestions.push(suggestion);
    }
    suggestions
}
```

### Using binary search

```rust
pub fn suggested_products(mut products: Vec<String>, search_word: String) -> Vec<Vec<String>> {
    products.sort_unstable();

    let mut answer = Vec::with_capacity(search_word.len());

    for len in 0..search_word.len() {
        let mut suggestions = Vec::with_capacity(3);
        let search_prefix = &search_word[0..=len];
        let start_from = products
            .binary_search_by(|x| x.as_str().cmp(search_prefix))
            .unwrap_or_else(|e| e);

        for idx in start_from..products.len().min(start_from + 3) {
            if !products[idx].starts_with(search_prefix) {
                break;
            }

            suggestions.push(products[idx].clone())
        }

        answer.push(suggestions);
    }

    answer
}
```

### Using a Trie

```rust
use std::collections::BTreeMap;

#[derive(Debug, Default)]
struct Node {
    values: BTreeMap<u8, Node>,
    word: Option<String>,
}

impl Node {
    pub fn insert(&mut self, word: String) {
        let mut node = self;

        for &b in word.as_bytes() {
            node = node.values.entry(b).or_default();
        }

        node.word = Some(word);
    }

    pub fn with_prefix(&self, prefix: &str, limit: usize) -> Vec<String> {
        let mut node = self;
        for b in prefix.as_bytes() {
            match node.values.get(b) {
                None => return vec![],
                Some(next) => node = next,
            }
        }

        let mut result = vec![];
        collect(node, &mut result, limit);
        result
    }
}

fn collect(node: &Node, dst: &mut Vec<String>, count: usize) {
    if dst.len() >= count {
        return;
    }

    if let Some(word) = &node.word {
        dst.push(word.clone());
        if dst.len() >= count {
            return;
        }
    }

    for x in node.values.values() {
        collect(x, dst, count);
        if dst.len() >= count {
            return;
        }
    }
}

pub fn suggested_products(products: Vec<String>, search_word: String) -> Vec<Vec<String>> {
    let mut trie = Node::default();
    products.into_iter().for_each(|word| trie.insert(word));

    let mut suggestions = vec![];
    for prefix_len in 1..=search_word.len() {
        let prefix = &search_word[..prefix_len];
        let words = trie.with_prefix(prefix, 3);
        suggestions.push(words);
    }

    suggestions
}
```
