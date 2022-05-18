# [1514. Path with Maximum Probability](https://leetcode.com/problems/path-with-maximum-probability/)

## Problem

### Description

You are given an undirected weighted graph of `n` nodes (0-indexed), represented
by an edge list where `edges[i] = [a, b]` is an undirected edge connecting the
nodes `a` and `b` with a probability of success of traversing that
edge `succProb[i]`.

Given two nodes `start` and `end`, find the path with the maximum probability of
success to go from `start` to `end` and return its success probability.

If there is no path from `start` to `end`, return `0`. Your answer will be
accepted if it differs from the correct answer by at most `1e-5`.

### Constraints

* `2 <= n <= 10^4`
* `0 <= start, end < n`
* `start != end`
* `0 <= a, b < n`
* `a != b`
* `0 <= succProb.length == edges.length <= 2*10^4`
* `0 <= succProb[i] <= 1`
* There is at most one edge between every two nodes.

### Examples

#### Example 1

![image](resources/1514/ex1.png)

```text
Input: n = 3, edges = [[0,1],[1,2],[0,2]], succProb = [0.5,0.5,0.2], start = 0, end = 2
Output: 0.25000
Explanation: There are two paths from start to end, one having a probability of success = 0.2 and the other has 0.5 * 0.5 = 0.25.
```

#### Example 2

![image](resources/1514/ex2.png)

```text
Input: n = 3, edges = [[0,1],[1,2],[0,2]], succProb = [0.5,0.5,0.3], start = 0, end = 2
Output: 0.30000
```

#### Example 3

![image](resources/1514/ex3.png)

```text
Input: n = 3, edges = [[0,1]], succProb = [0.5], start = 0, end = 2
Output: 0.00000
Explanation: There is no path between 0 and 2.
```

## Solutions

### Dijkstra

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;

#[derive(Debug, PartialEq, Copy, Clone, PartialOrd)]
struct F(f64);

impl Eq for F {}

impl Ord for F {
    fn cmp(&self, other: &Self) -> Ordering {
        self.partial_cmp(other).unwrap()
    }
}

pub fn max_probability(
    n: i32,
    edges: Vec<Vec<i32>>,
    succ_prob: Vec<f64>,
    start: i32,
    end: i32,
) -> f64 {
    assert!(n > 0);
    assert!(start >= 0);
    assert!(end >= 0);

    let mut visited = vec![false; n as usize];
    let mut graph = vec![vec![]; n as usize];

    for idx in 0..edges.len() {
        let edge = &edges[idx];
        graph[edge[0] as usize].push((edge[1] as usize, succ_prob[idx]));
        graph[edge[1] as usize].push((edge[0] as usize, succ_prob[idx]));
    }

    let mut pq = BinaryHeap::new();
    pq.push((F(1.0), start as usize));

    while let Some((F(probability), node)) = pq.pop() {
        if node == end as usize {
            return probability;
        }

        if visited[node] {
            continue;
        }
        visited[node] = true;

        for (next, p) in graph[node].iter().copied() {
            if visited[next] {
                continue;
            }

            pq.push((F(probability * p), next));
        }
    }

    0.0
}
```
