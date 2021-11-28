# [692. Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/)

## Problem

Given an array of strings `words` and an integer `k`, return the `k` most
frequent strings.

Return the answer sorted by the frequency from highest to lowest. Sort the words
with the same frequency by their lexicographical order.

#### Constraints

* `1 <= words.length <= 500`
* `1 <= words[i] <= 10`
* `words[i]` consists of lowercase English letters.
* `k` is in the range `[1, The number of unique words[i]]`

#### Examples

```text
Input: words = ["i","love","leetcode","i","love","coding"], k = 2
Output: ["i","love"]
Explanation: "i" and "love" are the two most frequent words.
Note that "i" comes before "love" due to a lower alphabetical order.
```

```text
Input: words = ["the","day","is","sunny","the","the","the","sunny","is","is"], k = 4
Output: ["the","is","sunny","day"]
Explanation: "the", "is", "sunny" and "day" are the four most frequent words, 
with the number of occurrence being 4, 3, 2 and 1 respectively.
```

## Solutions

### With Priority Queue

```rust
use std::cmp::Ordering;
use std::collections::{BinaryHeap, HashMap};
use std::option::Option::Some;

#[derive(Debug, Eq, PartialEq)]
struct Freq {
    word: String,
    count: usize,
}

impl Ord for Freq {
    fn cmp(&self, other: &Self) -> Ordering {
        match self.count.cmp(&other.count) {
            Ordering::Less => Ordering::Less,
            Ordering::Greater => Ordering::Greater,
            Ordering::Equal => self.word.cmp(&other.word).reverse(),
        }.reverse()
    }
}

impl PartialOrd for Freq {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

pub fn top_k_frequent(words: Vec<String>, k: i32) -> Vec<String> {
    let mut freq = HashMap::with_capacity(words.len());

    words.into_iter().for_each(|w| {
        freq.entry(w).and_modify(|c| *c += 1).or_insert(1usize);
    });

    let mut pq = BinaryHeap::with_capacity(k as usize + 1);
    freq.into_iter().for_each(|(w, c)| {
        let f = Freq { word: w, count: c };
        pq.push(f);
        if pq.len() > k as usize {
            let _ = pq.pop();
        }
    });

    let mut result = Vec::with_capacity(k as usize);
    while let Some(w) = pq.pop() {
        result.push(w.word);
    }
    result.reverse();
    result
}
```

### With Sorting

```rust
use std::collections::HashMap;

pub fn top_k_frequent(words: Vec<String>, k: i32) -> Vec<String> {
    let mut freq = HashMap::with_capacity(words.len());

    words.into_iter().for_each(|w| {
        freq.entry(w).and_modify(|c| *c += 1).or_insert(1usize);
    });

    let mut words: Vec<_> = freq.into_iter().collect();
    words.sort_unstable_by(|x, y| x.0.cmp(&y.0));
    words.sort_by(|x, y| x.1.cmp(&y.1).reverse());

    words.into_iter().take(k as usize).map(|(w, _)| w).collect()
}
```