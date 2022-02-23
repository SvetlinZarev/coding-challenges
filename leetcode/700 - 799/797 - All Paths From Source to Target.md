# [797. All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/)

## Problem

Given a directed acyclic graph (DAG) of `n` nodes labeled from `0` to `n - 1`,
find all possible paths from node `0` to node `n - 1` and return them in any
order.

The graph is given as follows: `graph[i]` is a list of all nodes you can visit
from node `i` (i.e., there is a directed edge from node `i` to
node `graph[i][j]`).

#### Example

Input:

```text
[[1,2],[3],[3],[]]
```

Output:

```text
[[0,1,3],[0,2,3]]
```

Explanation: There are two paths: `0 -> 1 -> 3` and `0 -> 2 -> 3`.

#### Constraints

* `n == graph.length`
* `2 <= n <= 15`
* `0 <= graph[i][j] < n`
* `graph[i][j] != i` (i.e., there will be no self-loops).
* All the elements of `graph[i]` are unique.
* The input graph is guaranteed to be a DAG.

## Solutions

### Using a DFS

* Time: 8ms
* Memory: 2.8 MB

```rust

pub fn all_paths_source_target(graph: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    let mut result = vec![];
    let mut buffer = vec![0];

    for root in 0..graph[0].len() {
        dfs(&graph, &mut result, &mut buffer, graph[0][root]);
    }

    result
}

fn dfs(graph: &Vec<Vec<i32>>, result: &mut Vec<Vec<i32>>, buf: &mut Vec<i32>, current: i32) {
    buf.push(current);

    if current as usize == graph.len() - 1 {
        result.push(buf.clone());
        buf.pop();

        return;
    }

    for root in graph[current as usize].iter().copied() {
        dfs(graph, result, buf, root);
    }

    buf.pop();
}
```

## Links and resources
* [Link](https://leetcode.com/problems/all-paths-from-source-to-target/)
  to the problem