# [127. Word Ladder](https://leetcode.com/problems/word-ladder/)

## Problem

A transformation sequence from word `beginWord` to word `endWord` using a
dictionary `wordList` is a sequence of
words `beginWord -> s1 -> s2 -> ... -> sk` such that:

* Every adjacent pair of words differs by a single letter.
* Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not
  need to be in `wordList`.
* `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return
the number of words in the shortest transformation sequence from `beginWord`
to `endWord`, or `0` if no such sequence exists.

#### Constraints

* `1 <= wordList.length <= 5000`
* `1 <= beginWord.length <= 10`
* `endWord.length == beginWord.length`
* `wordList[i].length == beginWord.length`
* `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English
  letters.
* `beginWord != endWord`
* All the words in `wordList` are unique.

#### Examples

```text
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.
```

```text
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.
```

## Solutions

### Graph + BFS

```rust
use std::collections::VecDeque;

const NO_PATH_FOUND: i32 = 0;

pub fn ladder_length(begin_word: String, end_word: String, mut word_list: Vec<String>) -> i32 {
    let mut start = None;
    let mut end = None;

    // make sure that the word list contains the begin/end words
    // then find their indexes
    for (idx, word) in word_list.iter().enumerate() {
        if begin_word.eq(word) {
            start = Some(idx);
        }

        if end_word.eq(word) {
            end = Some(idx);
        }

        if start.is_some() && end.is_some() {
            break;
        }
    }

    if end.is_none() {
        return NO_PATH_FOUND;
    }

    if start.is_none() {
        word_list.push(begin_word);
        start = Some(word_list.len() - 1);
    }

    let start = start.unwrap();
    let end = end.unwrap();

    // Build the adjacency lists
    let mut graph = vec![vec![]; word_list.len()];
    for i in 0..word_list.len() {
        for j in i + 1..word_list.len() {
            if is_adjacent(&word_list[i], &word_list[j]) {
                graph[i].push(j);
                graph[j].push(i);
            }
        }
    }

    // Run a BFS search
    let mut visited = vec![false; word_list.len()];
    let mut queue = VecDeque::new();

    // dist=1, because we count the words, not the transitions:
    // "cat" -> "bat" => 2 words, 1 transition
    queue.push_back((start, 1));

    while let Some((idx, dist)) = queue.pop_front() {
        if visited[idx] {
            continue;
        }

        if idx == end {
            return dist;
        }

        visited[idx] = true;
        graph[idx]
            .iter()
            .copied()
            .filter(|&idx| !visited[idx])
            .for_each(|i| queue.push_back((i, dist + 1)));
    }

    NO_PATH_FOUND
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

### Bidirectional search

```rust
use std::collections::HashSet;

const NO_PATH_FOUND: i32 = 0;

pub fn ladder_length(begin_word: String, end_word: String, mut word_list: Vec<String>) -> i32 {
    let mut start = None;
    let mut end = None;

    // make sure that the word list contains the begin/end words
    // then find their indexes
    for (idx, word) in word_list.iter().enumerate() {
        if begin_word.eq(word) {
            start = Some(idx);
        }

        if end_word.eq(word) {
            end = Some(idx);
        }

        if start.is_some() && end.is_some() {
            break;
        }
    }

    if end.is_none() {
        return NO_PATH_FOUND;
    }

    if start.is_none() {
        word_list.push(begin_word);
        start = Some(word_list.len() - 1);
    }

    let start = start.unwrap();
    let end = end.unwrap();

    // Build the adjacency lists
    let mut graph = vec![vec![]; word_list.len()];
    for i in 0..word_list.len() {
        for j in i + 1..word_list.len() {
            if is_adjacent(&word_list[i], &word_list[j]) {
                graph[i].push(j);
                graph[j].push(i);
            }
        }
    }

    // Run a bidirectional BFS search
    let mut visited = vec![false; word_list.len()];
    visited[start] = true;
    visited[end] = true;

    let mut left = HashSet::new();
    left.insert(start);

    let mut right = HashSet::new();
    right.insert(end);

    let mut neighbours = HashSet::new();

    let mut len = 1;
    while !left.is_empty() && !right.is_empty() {
        // make sure that "left" contains fewer elements
        if left.len() > right.len() {
            std::mem::swap(&mut left, &mut right);
        }

        for word in left.drain() {
            for neighbour in graph[word].iter().copied() {
                if right.contains(&neighbour) {
                    return len + 1;
                }

                if !visited[neighbour] {
                    visited[neighbour] = true;
                    neighbours.insert(neighbour);
                }
            }
        }

        std::mem::swap(&mut left, &mut neighbours);
        len += 1;
    }

    NO_PATH_FOUND
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

### Alternative (faster!) implementation of Bi-Directional BFS

It turns out that building the graph by using an O(n^2) algorithm, as in the
previous two implementations is very slow. For instance in a wordlist with 5000
words it takes around 4s to build the graph and less than 500 microseconds to
run the BFS. So this implementation exploits the fact that the ladder length is
very short, thus it's pretty cheap to run `is_adjacent()` inside the hot loop.

For example both previous implementations take around 150ms on LeetCode, while
this one takes around 20ms, which is 7-8 times faster.

```rust
use std::collections::HashSet;

pub fn ladder_length(begin_word: String, end_word: String, word_list: Vec<String>) -> i32 {
    if word_list.iter().find(|&w| end_word.eq(w)).is_none() {
        return 0;
    }

    let mut visited = HashSet::new();
    let mut left = HashSet::new();
    let mut right = HashSet::new();
    let mut next = HashSet::new();

    left.insert(begin_word.as_str());
    right.insert(end_word.as_str());
    visited.insert(begin_word.as_str());
    visited.insert(end_word.as_str());

    let mut len = 1;
    while !left.is_empty() && !right.is_empty() {
        if left.len() > right.len() {
            std::mem::swap(&mut left, &mut right);
        }

        for w in left.drain() {
            for n in word_list.iter().map(|s| s.as_str()) {
                if is_adjacent(w, n) {
                    if right.contains(n) {
                        return len + 1;
                    }

                    if visited.insert(n) {
                        next.insert(n);
                    }
                }
            }
        }

        std::mem::swap(&mut left, &mut next);
        len += 1;
    }

    0
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

* [126. Word Ladder II](126%20-%20Word%20Ladder%20II.md)
* [433. Minimum Genetic Mutation](/leetcode/400%20-%20499/433%20-%20Minimum%20Genetic%20Mutation.md)