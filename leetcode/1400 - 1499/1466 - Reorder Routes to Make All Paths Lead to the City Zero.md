# [1466. Reorder Routes to Make All Paths Lead to the City Zero](https://leetcode.com/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)

## Problem

### Description

There are `n` cities numbered from `0` to `n - 1` and `n - 1` roads such that
there is only one way to travel between two different cities (this network form
a tree). Last year, The ministry of transport decided to orient the roads in one
direction because they are too narrow.

Roads are represented by connections where `connections[i] = [ai, bi]`
represents a road from city `ai` to city `bi`.

This year, there will be a big event in the capital (city 0), and many people
want to travel to this city.

Your task consists of reorienting some roads such that each city can visit the
city 0. Return the minimum number of edges changed.

It's guaranteed that each city can reach city 0 after reorder.

### Constraints

* `2 <= n <= 5 * 10^4`
* `connections.length == n - 1`
* `connections[i].length == 2`
* `0 <= ai, bi <= n - 1`
* `ai != bi`

### Examples

#### Example 1

![image](resources/1466/ex1.png)

```text
Input: n = 6, connections = [[0,1],[1,3],[2,3],[4,0],[4,5]]
Output: 3
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

#### Example 2

![image](resources/1466/ex2.png)

```text
Input: n = 5, connections = [[1,0],[1,2],[3,2],[3,4]]
Output: 2
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

#### Example 3

```text
Input: n = 3, connections = [[1,0],[2,0]]
Output: 0
```

## Solutions

### DFS

#### Using an enum to track the edge direction (less efficient)

```rust
const START_NODE: usize = 0;

#[derive(Copy, Clone, Eq, PartialEq)]
enum Direction {
    Original,
    Reversed,
}

pub fn min_reorder(n: i32, connections: Vec<Vec<i32>>) -> i32 {
    let mut graph = vec![vec![]; n as usize];
    for edge in connections.into_iter() {
        graph[edge[0] as usize].push((edge[1] as usize, Direction::Original));
        graph[edge[1] as usize].push((edge[0] as usize, Direction::Reversed));
    }

    let mut stack = vec![];
    stack.push(START_NODE);

    let mut visited = vec![false; n as usize];
    visited[START_NODE] = true;

    let mut changes = 0;
    while let Some(from) = stack.pop() {
        for &(to, dir) in graph[from].iter() {
            if !visited[to] {
                visited[to] = true;

                if dir == Direction::Original {
                    changes += 1;
                }

                stack.push(to);
            }
        }
    }

    changes
}
```

#### Using the integer's sign to track the edge direction (more efficient)

```rust
const START_NODE: usize = 0;

pub fn min_reorder(n: i32, connections: Vec<Vec<i32>>) -> i32 {
    let mut graph = vec![vec![]; n as usize];
    for edge in connections.into_iter() {
        graph[edge[0] as usize].push(edge[1]);

        // Use a negative value to mark the reverse connection
        graph[edge[1] as usize].push(-edge[0]);
    }

    let mut stack = vec![];
    stack.push(START_NODE);

    let mut visited = vec![false; n as usize];
    visited[START_NODE] = true;

    let mut changes = 0;
    while let Some(from) = stack.pop() {
        for &to in graph[from].iter() {
            let to_node = to.abs() as usize;

            if !visited[to_node] {
                visited[to_node] = true;

                // Because the problem statement guarantees us a tree structure,
                // if we use the original edge, then the direction of the road
                // is pointing toward the leaf nodes - i.e. - away of the city,
                // so we need to reverse that connection
                if to > 0 {
                    changes += 1;
                }

                stack.push(to_node);
            }
        }
    }

    changes
}
```