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

const START_WORD_IDX: usize = 0;
const NO_PATH_FOUND: i32 = 0;

pub fn ladder_length(begin_word: String, end_word: String, mut word_list: Vec<String>) -> i32 {
    // Make sure that that the begin word is contained
    // in the `word_list` and that it's the first element
    let mut contains_begin_word = false;
    let mut contains_end_word = false;

    for idx in 0..word_list.len() {
        if word_list[idx] == begin_word {
            word_list.swap(START_WORD_IDX, idx);
            contains_begin_word = true;
        } else if word_list[idx] == end_word {
            contains_end_word = true;
        }

        if contains_begin_word & contains_end_word {
            break;
        }
    }

    if !contains_end_word {
        return NO_PATH_FOUND;
    }

    if !contains_begin_word {
        word_list.push(begin_word);
        let last_element_idx = word_list.len() - 1;
        word_list.swap(START_WORD_IDX, last_element_idx);
    }

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
    queue.push_back((START_WORD_IDX, 1));

    while let Some((idx, dist)) = queue.pop_front() {
        if visited[idx] {
            continue;
        }

        if word_list[idx] == end_word {
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

## Related problems

* [126. Word Ladder II](126%20-%20Word%20Ladder%20II.md)
* [433. Minimum Genetic Mutation](/leetcode/400%20-%20499/433%20-%20Minimum%20Genetic%20Mutation.md)