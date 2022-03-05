# [1768. Merge Strings Alternately](https://leetcode.com/problems/merge-strings-alternately/)

## Problem

### Description

You are given two strings `word1` and `word2`. Merge the strings by adding
letters in alternating order, starting with `word1`. If a string is longer than
the other, append the additional letters onto the end of the merged string.

Return the merged string.

### Constraints

* `1 <= word1.length, word2.length <= 100`
* `word1` and `word2` consist of lowercase English letters.

### Examples

```text
Input: word1 = "abc", word2 = "pqr"
Output: "apbqcr"
Explanation: The merged string will be merged as so:
word1:  a   b   c
word2:    p   q   r
merged: a p b q c r
```

```text
Input: word1 = "ab", word2 = "pqrs"
Output: "apbqrs"
Explanation: Notice that as word2 is longer, "rs" is appended to the end.
word1:  a   b 
word2:    p   q   r   s
merged: a p b q   r   s
```

```text
Input: word1 = "abcd", word2 = "pq"
Output: "apbqcd"
Explanation: Notice that as word1 is longer, "cd" is appended to the end.
word1:  a   b   c   d
word2:    p   q 
merged: a p b q c   d
```

## Solutions

```rust
pub fn merge_alternately(word1: String, word2: String) -> String {
    let a = word1.as_bytes();
    let b = word2.as_bytes();

    let mut merged = String::with_capacity(a.len() + b.len());
    for idx in 0..a.len().min(b.len()) {
        merged.push(a[idx] as char);
        merged.push(b[idx] as char);
    }

    for idx in a.len().min(b.len())..a.len() {
        merged.push(a[idx] as char);
    }
    for idx in a.len().min(b.len())..b.len() {
        merged.push(b[idx] as char);
    }

    merged
}
```