# [743. Network Delay Time](https://leetcode.com/problems/network-delay-time/)

## Problem

### Description

You are given a network of `n` nodes, labeled from `1` to `n`. You are also
given times, a list of travel times as directed edges `times[i] = (ui, vi, wi)`,
where `ui` is the source node, `vi` is the target node, and `wi` is the time it
takes for a signal to travel from source to target.

We will send a signal from a given node `k`. Return the time it takes for all
the `n` nodes to receive the signal. If it is impossible for all the `n` nodes
to receive the signal, return `-1`.

### Constraints

* `1 <= k <= n <= 100`
* `1 <= times.length <= 6000`
* `times[i].length == 3`
* `1 <= ui, vi <= n`
* `ui != vi`
* `0 <= wi <= 100`
* All the pairs `(ui, vi)` are unique. (i.e., no multiple edges.)

### Examples

```text
Input: times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
Output: 2
```

```text
Input: times = [[1,2,1]], n = 2, k = 1
Output: 1
```

```text
Input: times = [[1,2,1]], n = 2, k = 2
Output: -1
```

## Solutions

### BFS

```rust
use std::collections::VecDeque;

const UNVISITED_NODES: i32 = -1;

pub fn network_delay_time(times: Vec<Vec<i32>>, n: i32, k: i32) -> i32 {
    let mut graph = vec![vec![]; (n + 1) as usize];
    for time in times {
        graph[time[0] as usize].push((time[1] as usize, time[2]));
    }

    let mut queue = VecDeque::new();
    let mut delays = vec![None; (n + 1) as usize];
    let mut visited = 0;

    queue.push_back((k as usize, 0));

    while let Some((node, delay)) = queue.pop_front() {
        match delays[node] {
            None => {
                delays[node] = Some(delay);
                visited += 1;
            }

            Some(d) => {
                if d <= delay {
                    continue;
                }

                delays[node] = Some(delay);
            }
        }

        for (edge, weight) in graph[node].iter().copied() {
            queue.push_back((edge, delay + weight));
        }
    }

    if visited != n as usize {
        return UNVISITED_NODES;
    }

    delays
        .into_iter()
        // Skip the first node, because it's an unused, dummy node
        .skip(1)
        // it is safe to unwrap, because we already checked that all nodes have values
        .map(|x| x.unwrap_or(0))
        .max()
        .unwrap_or(0)
}
```

### DFS

In that problem the DFS solution is much slower than the BFS one, because it
ends-up updating the delays of a lot of nodes.

```rust
const UNVISITED_NODES: i32 = -1;

pub fn network_delay_time(times: Vec<Vec<i32>>, n: i32, k: i32) -> i32 {
    let mut graph = vec![vec![]; (n + 1) as usize];
    for time in times {
        graph[time[0] as usize].push((time[1] as usize, time[2]));
    }

    let mut stack = vec![];
    let mut delays = vec![None; (n + 1) as usize];
    let mut visited = 0;

    stack.push((k as usize, 0));

    while let Some((node, delay)) = stack.pop() {
        match delays[node] {
            None => {
                delays[node] = Some(delay);
                visited += 1;
            }

            Some(d) => {
                if d <= delay {
                    continue;
                }

                delays[node] = Some(delay);
            }
        }

        for (edge, weight) in graph[node].iter().copied() {
            stack.push((edge, delay + weight));
        }
    }

    if visited != n as usize {
        return UNVISITED_NODES;
    }

    delays
        .into_iter()
        // Skip the first node, because it's an unused, dummy node
        .skip(1)
        // it is safe to unwrap, because we already checked that all nodes have values
        .map(|x| x.unwrap_or(0))
        .max()
        .unwrap_or(0)
}
```

### Dijkstra-like algorithm (PriorityQueue)

In order to avoid the unnecessary updating of delay values, we can use a
priority queue to always pick the nodes with the smallest delay.

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

const UNVISITED_NODES: i32 = -1;

pub fn network_delay_time(times: Vec<Vec<i32>>, n: i32, k: i32) -> i32 {
    let mut graph = vec![vec![]; (n + 1) as usize];
    for time in times {
        graph[time[0] as usize].push((time[1] as usize, time[2]));
    }

    let mut queue = BinaryHeap::new();
    let mut delays = vec![None; (n + 1) as usize];
    let mut visited = 0;

    queue.push(Reverse((0, k as usize)));

    while let Some(Reverse((delay, node))) = queue.pop() {
        match delays[node] {
            // This node has not been visited before
            None => {
                delays[node] = Some(delay);
                visited += 1;

                // Because we always take the nodes with smallest delay first,
                // once we process all nodes at least once we can stop, because
                // all other delays in the queue will be larger than the the
                // current one
                if visited == n as usize {
                    break;
                }
            }

            // This node has already been visited via path with smaller delay
            Some(d) if d <= delay => {
                continue;
            }

            // This node has already been visited via path with larger delay,
            // so we update it to use the smaller one
            Some(d) => {
                delays[node] = Some(delay);
            }
        }

        for (edge, weight) in graph[node].iter().copied() {
            // This IF statement is optional. In some cases it can reduce the number of nodes
            // we push to the queue, but the algorithm should work fine without it.
            if delays[edge].is_none() || delays[edge] > Some(delay + weight) {
                queue.push(Reverse((delay + weight, edge)));
            }
        }
    }

    if visited != n as usize {
        return UNVISITED_NODES;
    }

    delays
        .into_iter()
        // Skip the first node, because it's an unused, dummy node
        .skip(1)
        // it is safe to unwrap, because we already checked that all nodes have values
        .map(|x| x.unwrap_or(0))
        .max()
        .unwrap_or(0)
}
```