# [126. Word Ladder II](https://leetcode.com/problems/word-ladder-ii/)

## Problem

A transformation sequence from word `beginWord` to word `endWord` using a
dictionary `wordList` is a sequence of
words `beginWord -> s1 -> s2 -> ... -> sk` such that:

* Every adjacent pair of words differs by a single letter.
* Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not
  need to be in `wordList`.
* `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return
all the shortest transformation sequences from `beginWord` to `endWord`, or an
empty list if no such sequence exists. Each sequence should be returned as a
list of the words `[beginWord, s1, s2, ..., sk]`.

#### Constraints

* `1 <= beginWord.length <= 5`
* `endWord.length == beginWord.length`
* `1 <= wordList.length <= 1000`
* `wordList[i].length == beginWord.length`
* `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English
  letters.
* `beginWord != endWord`
* All the words in wordList are unique.

#### Examples

```text
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: [["hit","hot","dot","dog","cog"],["hit","hot","lot","log","cog"]]
Explanation: There are 2 shortest transformation sequences:
"hit" -> "hot" -> "dot" -> "dog" -> "cog"
"hit" -> "hot" -> "lot" -> "log" -> "cog"
```

```text
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: []
Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.
```

## Solutions

### BFS + DFS

Because DFS does not workon graphs with cycles, first we run a BFS to find the
distances of the graph nodes from the start node. Then we run a DFS on the
resulting tree

```rust
use std::collections::hash_map::Entry;
use std::collections::{HashMap, VecDeque};

const START_WORD_IDX: usize = 0;

pub fn find_ladders(
    begin_word: String,
    end_word: String,
    word_list: Vec<String>,
) -> Vec<Vec<String>> {
    let word_list = match modify_word_list(&begin_word, &end_word, word_list) {
        None => return vec![],
        Some(list) => list,
    };

    let mut graph = build_graph(&word_list);
    let mut distances = HashMap::new();
    let shortest_path = match measure_node_distances(&graph, &mut distances) {
        None => return vec![],
        Some(len) => len,
    };

    // remove the unreachable nodes
    for list in graph.iter_mut() {
        list.retain(|i| distances.contains_key(i));
    }

    let mut solution = vec![];
    collect_paths(
        &graph,
        &word_list,
        &distances,
        shortest_path,
        &mut solution,
        &mut vec![begin_word],
        START_WORD_IDX,
    );
    solution
}

fn modify_word_list(
    begin_word: &str,
    end_word: &str,
    mut word_list: Vec<String>,
) -> Option<Vec<String>> {
    // Make sure that that the "begin word" is contained
    // in the `word_list` and that it's the first element
    // and the "wnd word" is contained at the last position
    let mut idx_begin = None;
    let mut idx_end = None;

    for idx in 0..word_list.len() {
        if idx_begin.is_none() && word_list[idx] == begin_word {
            idx_begin = Some(idx);
        } else if idx_end.is_none() && word_list[idx] == end_word {
            idx_end = Some(idx);
        }

        if idx_begin.is_some() & idx_end.is_some() {
            break;
        }
    }

    let mut idx_end = idx_end?;

    match idx_begin {
        None => {
            word_list.push(begin_word.to_owned());
            let last_element_idx = word_list.len() - 1;
            word_list.swap(START_WORD_IDX, last_element_idx);
            if START_WORD_IDX == idx_end {
                idx_end = last_element_idx;
            }
        }

        Some(idx) => {
            word_list.swap(START_WORD_IDX, idx);
            if START_WORD_IDX == idx_end {
                idx_end = idx;
            }
        }
    }

    let last_element_idx = word_list.len() - 1;
    word_list.swap(last_element_idx, idx_end);

    Some(word_list)
}

fn build_graph(word_list: &[String]) -> Vec<Vec<usize>> {
    let mut graph = vec![vec![]; word_list.len()];
    for i in 0..word_list.len() {
        for j in i + 1..word_list.len() {
            if is_adjacent(&word_list[i], &word_list[j]) {
                graph[i].push(j);
                graph[j].push(i);
            }
        }
    }
    graph
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

fn measure_node_distances(
    graph: &Vec<Vec<usize>>,
    distances: &mut HashMap<usize, usize>,
) -> Option<usize> {
    let mut queue = VecDeque::new();
    queue.push_back((START_WORD_IDX, 0));

    let mut shortest_path = None;

    while let Some((idx, dist)) = queue.pop_front() {
        if let Some(shortest_path) = shortest_path {
            if dist >= shortest_path {
                continue;
            }
        }

        match distances.entry(idx) {
            Entry::Occupied(_) => continue,
            Entry::Vacant(e) => e.insert(dist),
        };

        // the last index is the end-word
        if idx == graph.len() - 1 {
            shortest_path = Some(dist);
            continue;
        }

        graph[idx]
            .iter()
            .copied()
            .filter(|idx| !distances.contains_key(idx))
            .for_each(|i| queue.push_back((i, dist + 1)));
    }

    shortest_path
}

fn collect_paths(
    graph: &Vec<Vec<usize>>,
    word_list: &[String],
    distances: &HashMap<usize, usize>,
    shortest_path: usize,
    solution: &mut Vec<Vec<String>>,
    buf: &mut Vec<String>,
    last_word: usize,
) {
    if last_word == graph.len() - 1 {
        solution.push(buf.clone());
        return;
    }

    let distance = distances.get(&last_word).copied().unwrap();
    for &next_word in graph[last_word].iter() {
        if let Some(&d) = distances.get(&next_word) {
            if d == distance + 1 && d <= shortest_path {
                buf.push(word_list[next_word].clone());
                collect_paths(
                    graph,
                    word_list,
                    distances,
                    shortest_path,
                    solution,
                    buf,
                    next_word,
                );
                buf.pop();
            }
        }
    }
}
```

## Related problems

* [127. Word Ladder](/100%20-%20199/127%20-%20Word%20Ladder.md)