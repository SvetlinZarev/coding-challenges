# [433. Minimum Genetic Mutation](https://leetcode.com/problems/minimum-genetic-mutation/)

## Problem

A gene string can be represented by an 8-character long string, with choices
from 'A', 'C', 'G', and 'T'.

Suppose we need to investigate a mutation from a gene string `start` to a gene
string `end` where one mutation is defined as one single character changed in
the gene string.

* For example, `AACCGGTT -> AACCGGTA` is one mutation.

There is also a gene bank `bank` that records all the valid gene mutations. A
gene must be in `bank` to make it a valid gene string.

Given the two gene strings `start` and `end` and the gene bank `bank`, return
the minimum number of mutations needed to mutate from `start` to `end`. If there
is no such a mutation, return `-1`.

Note that the starting point is assumed to be valid, so it might not be included
in the `bank`.

#### Constraints

* `start.length == 8`
* `end.length == 8`
* `0 <= bank.length <= 10`
* `bank[i].length == 8`
* `start`, `end`, and `bank[i]` consist of only the
  characters `['A', 'C', 'G', 'T']`.

#### Examples

```text
Input: start = "AACCGGTT", end = "AACCGGTA", bank = ["AACCGGTA"]
Output: 1
```

```text
Input: start = "AACCGGTT", end = "AAACGGTA", bank = ["AACCGGTA","AACCGCTA","AAACGGTA"]
Output: 2
```

```text
Input: start = "AAAAACCC", end = "AACCCCCC", bank = ["AAAACCCC","AAACCCCC","AACCCCCC"]
Output: 3
```

## Solution

### Adjacency lists + BFS

```rust
use std::collections::VecDeque;

const MUTATION_NOT_POSSIBLE: i32 = -1;
const START_GENE: usize = 0;

pub fn min_mutation(start: String, end: String, mut bank: Vec<String>) -> i32 {
    // make sure that "start" is in the 0th position in "bank"
    let mut bank_contains_start = false;
    for idx in 0..bank.len() {
        if start == bank[idx] {
            bank.swap(START_GENE, idx);
            bank_contains_start = true;
            break;
        }
    }

    if !bank_contains_start {
        bank.push(start);
        let last = bank.len() - 1;
        bank.swap(START_GENE, last);
    }

    // Build the adjacency lists
    let mut graph = vec![vec![]; bank.len()];
    for i in 0..bank.len() {
        for j in i + 1..bank.len() {
            if is_adjacent(&bank[i], &bank[j]) {
                graph[i].push(j);
                graph[j].push(i);
            }
        }
    }

    // BFS
    let mut queue = VecDeque::new();
    let mut visited = vec![false; graph.len()];

    queue.push_back((START_GENE, 0));
    while let Some((idx, cost)) = queue.pop_front() {
        if visited[idx] {
            continue;
        }

        if end == bank[idx] {
            return cost;
        }

        visited[idx] = true;
        for &i in graph[idx].iter() {
            if !visited[i] {
                queue.push_back((i, cost + 1));
            }
        }
    }

    MUTATION_NOT_POSSIBLE
}

fn is_adjacent(a: &str, b: &str) -> bool {
    if a.len() != b.len() {
        return false;
    }

    let a = a.as_bytes();
    let b = b.as_bytes();

    let mut diffs = 0;
    for idx in 0..a.len() {
        if a[idx] != b[idx] {
            diffs += 1;

            if diffs > 1 {
                break;
            }
        }
    }

    diffs == 1
}
```

### Bidirectional BFS with word-list pruning

Doing the adjacency list computation outside the BFS search is slow, because we
always compute all N^2 possibilities. If we move it inside the search it would
still be `O(n^2)` but we may be able to stop earlier if we find a solution.
Also, we can progressively remove the processed words from the list, thus
further reducing the number of `is_adjacent()` calls.

```rust
use std::collections::HashSet;

const NO_SOLUTION: i32 = -1;

pub fn min_mutation(begin_word: String, end_word: String, mut word_list: Vec<String>) -> i32 {
    match word_list.iter().position(|w| end_word.eq(w)) {
        None => return NO_SOLUTION,
        Some(idx) => word_list.swap_remove(idx),
    };

    let mut left = HashSet::new();
    let mut right = HashSet::new();
    let mut next = HashSet::new();

    left.insert(begin_word);
    right.insert(end_word);

    let mut len = 0;
    while !left.is_empty() && !right.is_empty() {
        if left.len() > right.len() {
            std::mem::swap(&mut left, &mut right);
        }

        for w in left.drain() {
            let mut idx = 0;

            for word in right.iter() {
                if is_adjacent(&w, word.as_str()) {
                    return len + 1;
                }
            }

            while idx < word_list.len() {
                if !is_adjacent(&w, &word_list[idx]) {
                    idx += 1;
                    continue;
                }

                let neighbour = word_list.swap_remove(idx);
                next.insert(neighbour);
            }
        }

        std::mem::swap(&mut left, &mut next);
        len += 1;
    }

    NO_SOLUTION
}

fn is_adjacent(a: &str, b: &str) -> bool {
    let a = a.as_bytes();
    let b = b.as_bytes();
    assert_eq!(a.len(), b.len());

    let mut diffs = 0;
    for idx in 0..a.len() {
        if a[idx] != b[idx] {
            diffs += 1;
        }

        if diffs > 1 {
            break;
        }
    }

    diffs == 1
}
```

## Related problems

* [127. Word Ladder](/leetcode/100%20-%20199/127%20-%20Word%20Ladder.md)