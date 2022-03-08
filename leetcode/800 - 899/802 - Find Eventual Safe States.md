# [802. Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)

## Problem

### Description

There is a directed graph of `n` nodes with each node labeled from `0`
to `n - 1`. The graph is represented by a 0-indexed 2D integer array `graph`
where `graph[i]` is an integer array of nodes adjacent to node `i`, meaning
there is an edge from node `i` to each node in `graph[i]`.

A node is a terminal node if there are no outgoing edges. A node is a safe node
if every possible path starting from that node leads to a terminal node.

Return an array containing all the safe nodes of the graph. The answer should be
sorted in ascending order.

### Constraints

* `n == graph.length`
* `1 <= n <= 10^4`
* `0 <= graph[i].length <= n`
* `0 <= graph[i][j] <= n - 1`
* `graph[i]` is sorted in a strictly increasing order.
* The graph may contain self-loops.
* The number of edges in the graph will be in the range `[1, 4 * 104]`.

### Examples

#### Example 1

![image](resources/802/ex1.png)

```text
Input: graph = [[1,2],[2,3],[5],[0],[5],[],[]]
Output: [2,4,5,6]
Explanation: The given graph is shown above.
Nodes 5 and 6 are terminal nodes as there are no outgoing edges from either of them.
Every path starting at nodes 2, 4, 5, and 6 all lead to either node 5 or 6.
```

#### Example 2

```text
Input: graph = [[1,2,3,4],[1,2],[3,4],[0,4],[]]
Output: [4]
Explanation:
Only node 4 is a terminal node, and every path starting at node 4 leads to node 4.
```

## Solutions

### Reverse graph

* All terminal nodes are safe nodes
* All nodes whose outgoing edges lead to safe nodes are also safe nodes

```rust
use std::collections::{HashSet, VecDeque};

pub fn eventual_safe_nodes(edges: Vec<Vec<i32>>) -> Vec<i32> {
    let mut graph = vec![HashSet::new(); edges.len()];
    let mut rgraph = vec![HashSet::new(); edges.len()];

    // build the graph and the reversed graph
    // from the given edges
    for (from, to) in edges.into_iter().enumerate() {
        for &node in to.iter() {
            graph[from].insert(node as usize);
        }
        for &node in to.iter() {
            rgraph[node as usize].insert(from);
        }
    }

    // Add the terminal nodes in the queue. All terminal nodes are 
    // also safe nodes, therefore this queue will contain only safe nodes
    let mut queue = VecDeque::new();
    for node in 0..graph.len() {
        if graph[node].is_empty() {
            queue.push_back(node);
        }
    }

    let mut node_safety = vec![false; graph.len()];

    while let Some(safe_node) = queue.pop_front() {
        node_safety[safe_node] = true;

        for &neighbour in rgraph[safe_node].iter() {
            // remove the edge to the current (i.e. safe) node
            graph[neighbour].remove(&safe_node);

            // If there are no more edges to other nodes it means
            // that all edges are pointing to safe nodes, therefore
            // this node is also a safe node. In that case push it to
            // the queue in order to mark it.
            if graph[neighbour].is_empty() {
                queue.push_back(neighbour);
            }
        }
    }

    node_safety
        .into_iter()
        .enumerate()
        .filter_map(|(idx, safe)| match safe {
            true => Some(idx as i32),
            false => None,
        })
        .collect()
}
```

### DFS

We basically do a cycle detection:

* Initially all nodes are `Unvisited`
* We mark a node as `Undecided` the first time we see it
* If we have already visited the node => return if it's a safe node
* If the node does not have any neighbours - > mark it as safe node
* Recursively check all it's neighbours -> it they are all safe nodes, then this
  is a safe node as well
* If we reach an `Undecided` node, then we've reached a cycle => therefore this
  is not a safe node

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum Color {
    NotVisited,
    Undecided,
    Safe,
}

pub fn eventual_safe_nodes(graph: Vec<Vec<i32>>) -> Vec<i32> {
    let mut color = vec![Color::NotVisited; graph.len()];
    let mut answer = vec![];
    for node in 0..graph.len() {
        if dfs(&mut color, &graph, node) {
            answer.push(node as i32);
        }
    }
    answer
}


fn dfs(color: &mut [Color], graph: &[Vec<i32>], node: usize) -> bool {
    if color[node] != Color::NotVisited {
        return color[node] == Color::Safe;
    }

    color[node] = Color::Undecided;
    for &neighbour in graph[node].iter() {
        // we've reached a cycle -> not a safe node
        if color[neighbour as usize] == Color::Undecided {
            return false;
        }

        // some of its neighbours is not a safe node -> this is also not a safe node
        if !dfs(color, graph, neighbour as usize) {
            return false;
        }
    }

    // This node will be marked as safe, only if all its neighbours are also safe
    color[node] = Color::Safe;
    true
}
```