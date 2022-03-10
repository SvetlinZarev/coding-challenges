# [886. Possible Bipartition](https://leetcode.com/problems/possible-bipartition/)

## Problem

### Description

We want to split a group of `n` people (labeled from `1` to `n`) into two groups
of any size. Each person may dislike some other people, and they should not go
into the same group.

Given the integer `n` and the array `dislikes` where `dislikes[i] = [ai, bi]`
indicates that the person labeled `ai` does not like the person labeled `bi`,
return `true` if it is possible to split everyone into two groups in this way.

### Constraints

* `1 <= n <= 2000`
* `0 <= dislikes.length <= 10^4`
* `dislikes[i].length == 2`
* `1 <= dislikes[i][j] <= n`
* `ai < bi`
* All the pairs of `dislikes` are unique.

### Examples

```text
Input: n = 4, dislikes = [[1,2],[1,3],[2,4]]
Output: true
Explanation: group1 [1,4] and group2 [2,3].
```

```text
Input: n = 3, dislikes = [[1,2],[1,3],[2,3]]
Output: false
```

```text
Input: n = 5, dislikes = [[1,2],[2,3],[3,4],[4,5],[1,5]]
Output: false
```

## Solutions

### DFS coloring of nodes (odd/even length cycle detection)

In order for the graph to be bipartite, a node's edges must all lead to nodes of
the opposite color. This can happen if and only if:

* there are no cycles in the graph
* all cycles must be of even length
    * note that the cycle length is measured by the number of nodes (vertices)
    * imagine the odd length `1 -> 2 -> 3 -> 1` graph (odd, because it has 3
      nodes), if we try to color it like `red -> blue -> red -> (blue)`, we see
      that the `1` must be both `red` and `blue` thus the graph is not bipartite

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum Color {
    SetA,
    SetB,
}

impl Color {
    fn opposite(self) -> Color {
        match self {
            Color::SetA => Color::SetB,
            Color::SetB => Color::SetA,
        }
    }
}

pub fn possible_bipartition(n: i32, dislikes: Vec<Vec<i32>>) -> bool {
    let mut graph = vec![vec![]; n as usize];
    for edge in dislikes.into_iter() {
        graph[edge[0] as usize - 1].push(edge[1] - 1);
        graph[edge[1] as usize - 1].push(edge[0] - 1);
    }

    is_bipartite(graph)
}

pub fn is_bipartite(graph: Vec<Vec<i32>>) -> bool {
    let mut color = vec![None; graph.len()];
    for node in 0..graph.len() {
        // iterate over all nodes, because the graph may not be "connected"
        if !color_nodes_dfs(&graph, &mut color, node, None) {
            return false;
        }
    }
    true
}

fn color_nodes_dfs(
    graph: &[Vec<i32>],
    color: &mut [Option<Color>],
    node: usize,
    prev: Option<Color>,
) -> bool {
    if let Some(current) = color[node] {
        return match prev {
            // None, means that we have already processed this node
            // in another iteration of the cycle in `is_bipartite`
            None => true,
            // If both colors are equal, we have detected a cycle of odd length,
            // otherwise we have detected a cycle of even length
            Some(prev) => current != prev,
        };
    }

    // We have not visited this node, so color it either using the opposite of
    // the previous node's color if any, or use the default value
    let current = *color[node].insert(match prev {
        // note: insert returns a pointer to the inserted value!
        None => Color::SetA,
        Some(c) => c.opposite(),
    });

    // recursive coloring & cycle detection
    for next in graph[node].iter().map(|&x| x as usize) {
        if !color_nodes_dfs(graph, color, next, Some(current)) {
            // an odd length cycle was detected
            return false;
        }
    }

    true
}
```

## Related Problems

* [785. Is Graph Bipartite](/leetcode/700%20-%20799/785%20-%20Is%20Graph%20Bipartite.md)