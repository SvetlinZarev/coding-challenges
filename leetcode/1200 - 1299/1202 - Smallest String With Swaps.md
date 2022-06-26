# [1202. Smallest String With Swaps](https://leetcode.com/problems/smallest-string-with-swaps/)

## Problem

### Description

You are given a string `s`, and an array of pairs of indices in the
string `pairs` where `pairs[i] = [a, b]` indicates 2 indices(0-indexed) of the
string.

You can swap the characters at any pair of indices in the given pairs any number
of times.

Return the lexicographically smallest string that `s` can be changed to after
using the swaps.

### Constraints

* `1 <= s.length <= 10^5`
* `0 <= pairs.length <= 10^5`
* `0 <= pairs[i][0], pairs[i][1] < s.length`
* `s` only contains lower case English letters.

### Examples

```text
Input: s = "dcab", pairs = [[0,3],[1,2]]
Output: "bacd"
Explanation: 
Swap s[0] and s[3], s = "bcad"
Swap s[1] and s[2], s = "bacd"
```

```text
Input: s = "dcab", pairs = [[0,3],[1,2],[0,2]]
Output: "abcd"
Explanation: 
Swap s[0] and s[3], s = "bcad"
Swap s[0] and s[2], s = "acbd"
Swap s[1] and s[2], s = "abcd"
```

```text
Input: s = "cba", pairs = [[0,1],[1,2]]
Output: "abc"
Explanation: 
Swap s[0] and s[1], s = "bca"
Swap s[1] and s[2], s = "bac"
Swap s[0] and s[1], s = "abc"
```

## Solutions

### Representing the connected characters as a disconnected graph

**Observation:** all connected components can be sorted. Let's
have `pairs=[[0,1], [1, 2]]`. Although there is no connection between 0 and 2,
we can still swap them by first swapping 1 and 2. This is similar to how
bubble-sort works.

```rust
pub fn smallest_string_with_swaps(s: String, pairs: Vec<Vec<i32>>) -> String {
    let mut s = s.into_bytes();
    let mut answer = vec![0u8; s.len()];

    // Create an adjacency list based graph
    let mut graph = vec![vec![]; s.len()];
    for pair in pairs.iter() {
        graph[pair[0] as usize].push(pair[1] as usize);
        graph[pair[1] as usize].push(pair[0] as usize);
    }

    let mut idxs = vec![];
    let mut chars = vec![];
    let mut stack = vec![];

    // Iterate over each node, because the graph may be disconnected
    for start in 0..graph.len() {
        if s[start] == 0 {
            // Skip over the already processed characters
            continue;
        }

        if graph[start].is_empty() {
            // Copy the characters that are not connected to any other character
            answer[start] = s[start];
            continue;
        }

        // Reset the buffers for indexes, characters and the DFS stack
        // in order to reuse them instead of allocating new ones
        idxs.clear();
        chars.clear();
        stack.push(start);

        // Collect all paired characters and their indexes
        while let Some(idx) = stack.pop() {
            if s[idx] == 0 {
                // already processed
                continue;
            }

            idxs.push(idx);
            chars.push(s[idx]);
            s[idx] = 0;

            for x in graph[idx].iter().copied() {
                if s[x] == 0 {
                    // already processed
                    continue;
                }

                stack.push(x);
            }
        }

        assert_eq!(chars.len(), idxs.len());
        chars.sort_unstable();
        idxs.sort_unstable();

        // put the connected characters at their final positions
        idxs.iter()
            .zip(chars.iter())
            .for_each(|(&idx, &ch)| answer[idx] = ch);
    }

    String::from_utf8(answer).unwrap()
}
```

### Union-Find

TODO