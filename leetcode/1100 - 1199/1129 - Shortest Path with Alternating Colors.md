# [1129. Shortest Path with Alternating Colors](https://leetcode.com/problems/shortest-path-with-alternating-colors/)

## Problem

### Description

You are given an integer `n`, the number of nodes in a directed graph where the
nodes are labeled from `0` to `n - 1`. Each edge is `red` or `blue` in this
graph, and there could be self-edges and parallel edges.

You are given two arrays `redEdges` and `blueEdges` where:

* `redEdges[i] = [ai, bi]` indicates that there is a directed red edge from
  node `ai` to node `bi` in the graph, and
* `blueEdges[j] = [uj, vj]` indicates that there is a directed blue edge from
  node `uj` to node `vj` in the graph.

Return an array answer of length `n`, where each `answer[x]` is the length of
the shortest path from node `0` to node `x` such that the edge colors alternate
along the path, or `-1` if such a path does not exist.

### Constraints

* `1 <= n <= 100`
* `0 <= redEdges.length, blueEdges.length <= 400`
* `redEdges[i].length == blueEdges[j].length == 2`
* `0 <= ai, bi, uj, vj < n`

### Examples

```text
Input: n = 3, redEdges = [[0,1],[1,2]], blueEdges = []
Output: [0,1,-1]
```

```text
Input: n = 3, redEdges = [[0,1]], blueEdges = [[2,1]]
Output: [0,1,-1]
```

## Solutions

### BFS + bit-flags

Main Ideas:

* Use a BFS to find the shortest distance.
* Mark the visited nodes using bitmasks for red/blue in order to save space.
* Keep track of the current node color in order to know whether to process the
  blue or red edges next.

```rust
const BITFLAG_BLUE: u8 = 1 << 0;
const BITFLAG_RED: u8 = 1 << 1;

pub fn shortest_alternating_paths(
    n: usize,
    red_edges: Vec<Vec<i32>>,
    blue_edges: Vec<Vec<i32>>,
) -> Vec<i32> {
    let mut red = vec![vec![]; n];
    for edge in red_edges.into_iter() {
        red[edge[0] as usize].push(edge[1] as usize);
    }

    let mut blue = vec![vec![]; n];
    for edge in blue_edges.into_iter() {
        blue[edge[0] as usize].push(edge[1] as usize);
    }

    let mut distances = vec![-1; n];
    let mut visted = vec![0; n];

    let mut queue = vec![];
    let mut next = vec![];
    let mut dist = 0;

    queue.push((0, BITFLAG_BLUE));
    queue.push((0, BITFLAG_RED));
    visted[0] = BITFLAG_BLUE | BITFLAG_RED;

    while !queue.is_empty() {
        while let Some((node, color)) = queue.pop() {
            if distances[node] < 0 {
                distances[node] = dist;
            }

            // If the current node is BLUE, the next one must be RED and vice-versa
            let (bitmask, graph) = if color == BITFLAG_BLUE {
                (BITFLAG_RED, &red)
            } else {
                (BITFLAG_BLUE, &blue)
            };

            for &n in graph[node].iter() {
                if visted[n] & bitmask == 0 {
                    visted[n] |= bitmask;
                    next.push((n, bitmask));
                }
            }
        }

        std::mem::swap(&mut queue, &mut next);
        dist += 1;
    }

    distances
}
```