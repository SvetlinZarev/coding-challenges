# [847. Shortest Path Visiting All Nodes](https://leetcode.com/problems/shortest-path-visiting-all-nodes/)

## Problem

### Description

You have an undirected, connected graph of `n` nodes labeled from `0` to `n - 1`
. You are given an array `graph` where `graph[i]` is a list of all the nodes
connected with node `i` by an edge.

Return the length of the shortest path that visits every node. You may start and
stop at any node, you may revisit nodes multiple times, and you may reuse edges.

### Constraints

* `n == graph.length`
* `1 <= n <= 12`
* `0 <= graph[i].length < n`
* `graph[i]` does not contain `i`.
* If `graph[a]` contains `b`, then `graph[b]` contains `a`.
* The input graph is always connected.

### Examples

![image](/leetcode/800%20-%20899/resources/847/example-1.jpg)

```text
Input: graph = [[1,2,3],[0],[0],[0]]
Output: 4
Explanation: One possible path is [1,0,2,0,3]
```

![image](/leetcode/800%20-%20899/resources/847/example-2.jpg)

```text
Input: graph = [[1],[0,2,4],[1,3,4],[2],[1,2]]
Output: 4
Explanation: One possible path is [0,1,4,2,3]
```

## Solutions

### BFS from each node

Run a BFS, starting from each node in the graph. Use a bitset to keep track of
the visited nodes.

```rust
pub fn shortest_path_length(graph: Vec<Vec<i32>>) -> i32 {
    assert!(graph.len() <= 16);
    if graph.len() <= 1 {
        return 0;
    }

    let mask_all_visited = (1u16 << graph.len()) - 1;
    let mut seen = vec![vec![false; mask_all_visited as usize]; graph.len()];
    let mut queue = vec![];

    for node in 0..graph.len() {
        let node_idx = node as u16;
        let mask_visited = (1 << node_idx) as u16;

        queue.push((node_idx, mask_visited));
        seen[node_idx as usize][mask_visited as usize] = true;
    }

    let mut steps = 0;
    let mut next_round = vec![];

    while !queue.is_empty() {
        while let Some((node, visited)) = queue.pop() {
            for &neighbour in graph[node as usize].iter() {
                let next_visited = visited | (1 << neighbour);
                if next_visited == mask_all_visited {
                    return steps + 1;
                }

                if !seen[neighbour as usize][next_visited as usize] {
                    seen[neighbour as usize][next_visited as usize] = true;
                    next_round.push((neighbour as u16, next_visited));
                }
            }
        }
        std::mem::swap(&mut queue, &mut next_round);
        steps += 1;
    }

    -1
}
```