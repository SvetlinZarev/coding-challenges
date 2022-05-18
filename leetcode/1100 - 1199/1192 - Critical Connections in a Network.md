# [1192. Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/)

## Problem

### Description

There are `n` servers numbered from `0` to `n - 1` connected by undirected
server-to-server connections forming a network where `connections[i] = [ai, bi]`
represents a connection between servers `ai` and `bi`. Any server can reach
other servers directly or indirectly through the network.

A critical connection is a connection that, if removed, will make some servers
unable to reach some other server.

Return all critical connections in the network in any order.

### Constraints

* `2 <= n <= 10^5`
* `n - 1 <= connections.length <= 10^5`
* `0 <= ai, bi <= n - 1`
* `ai != bi`
* `There are no repeated connections.

### Examples

![image](resources/1192/ex1.png)

```text
Input: n = 4, connections = [[0,1],[1,2],[2,0],[1,3]]
Output: [[1,3]]
Explanation: [[3,1]] is also accepted.
```

```text
Input: n = 2, connections = [[0,1]]
Output: [[0,1]]
```

## Solutions

### Bruteforce DFS (Time limit Exceeded)

For each edge between the nodes, take one edge and remove it (or skip it) from
the graph. Start traversing from the first node of the edge and check if the
second node is reachable. If it is, then this edge is not "critical"

```rust
pub fn critical_connections(n: i32, connections: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    assert!(n > 0);

    let mut graph = vec![vec![]; n as usize];
    for connection in connections.iter() {
        graph[connection[0] as usize].push(connection[1] as usize);
        graph[connection[1] as usize].push(connection[0] as usize);
    }

    let mut visited = vec![false; n as usize];
    let mut answer = vec![];

    for connection in connections.into_iter() {
        visited.fill(false);

        let node_1 = connection[0] as usize;
        let node_2 = connection[1] as usize;

        // The initial node and edge_start must be the same node
        if !is_node_reachable(&graph, &mut visited, node_1, node_1, node_2) {
            answer.push(connection);
        }
    }

    answer
}

fn is_node_reachable(
    graph: &[Vec<usize>],
    visited: &mut [bool],
    // current node
    node: usize,
    // nodes from the edge we decided to skip:  but since we always start
    // with the edge_start node, we only have to look for the edge_end node
    edge_start: usize,
    edge_end: usize,
) -> bool {
    if visited[node] {
        return false;
    }
    visited[node] = true;

    for &child in graph[node].iter() {
        // skip the selected edge from the graph
        if node == edge_start && child == edge_end {
            continue;
        }

        // skip visited nodes in order to avoid cycles
        if visited[child] {
            continue;
        }

        // Given that we:
        // * started from edge_start
        // * skipped the edge edge_start -> edge_end
        // * yet we still reached edge_end
        // means that this edge is not a bridge
        if child == edge_end {
            return true;
        }

        if is_node_reachable(graph, visited, child, edge_end, edge_end) {
            return true;
        }
    }

    false
}
```

### Tarjan's (bridge-finding) algorithm

```rust
pub fn critical_connections(n: i32, connections: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    assert!(n > 0);

    let mut graph = vec![vec![]; n as usize];
    for connection in connections.iter() {
        graph[connection[0] as usize].push(connection[1] as usize);
        graph[connection[1] as usize].push(connection[0] as usize);
    }

    let mut answer = vec![];
    let mut discovery = vec![0; n as usize];
    let mut earliest = vec![0; n as usize];

    dfs(
        &graph,
        &mut answer,
        &mut discovery,
        &mut earliest,
        1,
        n as usize,
        0,
    );

    answer
}

fn dfs(
    graph: &[Vec<usize>],
    answer: &mut Vec<Vec<i32>>,
    discovery: &mut [usize],
    earliest: &mut [usize],
    counter: usize,
    parent: usize,
    node: usize,
) {
    // Add a guard to avoid visiting a node for a second time
    // if discovery[node] != 0 {
    //     return;
    // }

    discovery[node] = counter;
    earliest[node] = counter;

    for &child in graph[node].iter() {
        if child == parent {
            continue;
        }

        // a discovery time of 0, means that the node has not been visited yet
        if discovery[child] == 0 {
            dfs(graph, answer, discovery, earliest, counter + 1, node, child);

            earliest[node] = earliest[node].min(earliest[child]);
            if discovery[node] < earliest[child] {
                answer.push(vec![node as i32, child as i32]);
            }
        } else {
            earliest[node] = earliest[node].min(discovery[child]);
        }
    }
}
```
