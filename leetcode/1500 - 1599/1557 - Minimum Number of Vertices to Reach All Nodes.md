# [1557. Minimum Number of Vertices to Reach All Nodes](https://leetcode.com/problems/minimum-number-of-vertices-to-reach-all-nodes/)

## Problem

### Description

Given a directed acyclic graph, with `n` vertices numbered from `0` to `n-1`,
and an array `edges` where `edges[i] = [fromi, toi]` represents a directed edge
from node `fromi` to node `toi`.

Find the smallest set of vertices from which all nodes in the graph are
reachable. It's guaranteed that a unique solution exists.

Notice that you can return the vertices in any order.

### Constraints

* `2 <= n <= 10^5`
* `1 <= edges.length <= min(10^5, n * (n - 1) / 2)`
* `edges[i].length == 2`
* `0 <= fromi, toi < n`
* All pairs `(fromi, toi)` are distinct.

### Examples

![image](resources/1557/ex1.png)

```text
Input: n = 6, edges = [[0,1],[0,2],[2,5],[3,4],[4,2]]
Output: [0,3]
Explanation: It's not possible to reach all the nodes from a single vertex. 
From 0 we can reach [0,1,2,5]. From 3 we can reach [3,4,2,5]. So we output [0,3].
```

![image](resources/1557/ex2.png)

```text
Input: n = 5, edges = [[0,1],[2,1],[3,1],[1,4],[2,4]]
Output: [0,2,3]
Explanation: Notice that vertices 0, 3 and 2 are not reachable from any other 
node, so we must include them. Also any of these vertices can reach nodes 1 and 4.
```

## Solutions

### DFS

We can run DFS starting from each node and count how many times we have reached
any node. Then the minimal set is the set of nodes which have a count of 1

### Find the nodes that don't have any incoming edges

Observations:

* If a node has an incoming edge, that it's reachable from another node, thus
  it's not part of the answer
* If a node does not have any incoming edges, that means it's NOT reachable from
  any other node, thus it is part of the answer

This means that the smallest set of nodes from which we can reach all other
nodes is the set of nodes with indegree of 0

```rust
pub fn find_smallest_set_of_vertices(n: usize, edges: Vec<Vec<i32>>) -> Vec<i32> {
    let mut incoming = vec![0; n];
    for edge in edges.into_iter() {
        let to = edge[1] as usize;
        incoming[to] += 1;
    }

    incoming
        .into_iter()
        .enumerate()
        .filter_map(|(node, inc)| match inc {
            0 => Some(node as i32),
            _ => None,
        })
        .collect()
}
```