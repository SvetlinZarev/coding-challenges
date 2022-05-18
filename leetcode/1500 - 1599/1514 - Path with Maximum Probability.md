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

In general the Dijkstra algorithm is applicable when:

* there are no negative weights
* we are interested in finding the **minimal** cost (the algorithm does not work
  for maximal cost)

But here we are searching for the "maximal probability". So why does dijkstra
work here ? The reason is that the Dijkstra algorithm requires the cost to get "
monotonically worse". And because all probabilities are between 0 and 1, every
multiplication reduces the probability. Or said in other words - in this case,
just as in the general case, the routes get more expensive as we go.

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;

#[derive(Debug, PartialEq, Copy, Clone, PartialOrd)]
struct NonInfNan(f64);

impl Eq for NonInfNan {}

impl Ord for NonInfNan {
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
    pq.push((NonInfNan(0.0), start as usize));

    while let Some((NonInfNan(probability), node)) = pq.pop() {
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

            pq.push((NonInfNan(probability * p), next));
        }
    }

    0.0
}
```

### Dijkstra + logarithms

```rust
use std::cmp::{Ordering, Reverse};
use std::collections::BinaryHeap;

#[derive(Debug, PartialEq, Copy, Clone, PartialOrd)]
struct NonInfNan(f64);

impl Eq for NonInfNan {}

impl Ord for NonInfNan {
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
        // Because Dijkstra is used for finding the "shortest" path, we can convert
        // the probabilities from the [0.0..1.0] domain to the log domain. Because
        // all probabilities are <= 1, the log of probabilities will be negative.
        //
        // Thus if we negate them,the higher probability will ead to smaller cost,
        // and the smaller probability will lead to higher cost:
        // * log10(0.1) = -1 => cost=1
        // * log10(0.9) = −0,04.. => cost=0.04..
        //
        // In the probabilities domain we multiply the probabilities, but in the
        // logarithms domain we have to sum it because log(A*B) = log(A) + log(B)
        let log_cost = -succ_prob[idx].log10();
        graph[edge[0] as usize].push((edge[1] as usize, log_cost));
        graph[edge[1] as usize].push((edge[0] as usize, log_cost));
    }

    let mut pq = BinaryHeap::new();
    pq.push((Reverse(NonInfNan(0.0)), start as usize));

    while let Some((Reverse(NonInfNan(cost)), node)) = pq.pop() {
        if node == end as usize {
            // Transition from the logarithms domain to the probabilities domain
            return 10.0f64.powf(-cost);
        }

        if visited[node] {
            continue;
        }
        visited[node] = true;

        for (next, c) in graph[node].iter().copied() {
            if visited[next] {
                continue;
            }

            pq.push((Reverse(NonInfNan(cost + c)), next));
        }
    }

    0.0
}
```
