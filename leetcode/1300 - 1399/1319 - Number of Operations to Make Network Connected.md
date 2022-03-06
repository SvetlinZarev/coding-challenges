# [1319. Number of Operations to Make Network Connected](https://leetcode.com/problems/number-of-operations-to-make-network-connected/)

## Problem

There are `n` computers numbered from `0` to `n - 1` connected by ethernet
cables connections forming a network where `connections[i] = [ai, bi]`
represents a connection between computers `ai` and `bi`. Any computer can reach
any other computer directly or indirectly through the network.

You are given an initial computer network `connections`. You can extract certain
cables between two directly connected computers, and place them between any pair
of disconnected computers to make them directly connected.

Return the minimum number of times you need to do this in order to make all the
computers connected. If it is not possible, return `-1`.

### Constraints

* `1 <= n <= 10^5`
* `1 <= connections.length <= min(n * (n - 1) / 2, 10^5)`
* `connections[i].length == 2`
* `0 <= ai, bi < n`
* `ai != bi`
* There are no repeated connections.
* No two computers are connected by more than one cable.

### Examples

#### Example 1

![image](resources/1319/ex1.png)

```text
Input: n = 4, connections = [[0,1],[0,2],[1,2]]
Output: 1
Explanation: Remove cable between computer 1 and 2 and place between computers 1 and 3.
```

#### Example 2

![image](resources/1319/ex2.png)

```text
Input: n = 6, connections = [[0,1],[0,2],[0,3],[1,2],[1,3]]
Output: 2
```

#### Example 3

```text
Input: n = 6, connections = [[0,1],[0,2],[0,3],[1,2]]
Output: -1
Explanation: There are not enough cables.
```

## Solution

### DFS

```rust
pub fn make_connected(n: usize, connections: Vec<Vec<i32>>) -> i32 {
    let mut graph = vec![(false, vec![]); n];
    for conn in connections.into_iter() {
        let src = conn[0] as usize;
        let dst = conn[1] as usize;
        graph[src].1.push(dst);
        graph[dst].1.push(src);
    }

    let mut forests = 0;
    let mut spare_wires = 0;

    for idx in 0..graph.len() {
        if !graph[idx].0 {
            forests += 1;
            spare_wires += mark_forest_and_count_spare_wires(&mut graph, idx);
        }
    }

    if forests - 1 > spare_wires {
        return -1;
    }

    forests - 1
}

fn mark_forest_and_count_spare_wires(graph: &mut Vec<(bool, Vec<usize>)>, start: usize) -> i32 {
    let mut stack = vec![start];

    let mut spare_wires = 0;
    while let Some(node) = stack.pop() {
        if graph[node].0 {
            spare_wires += 1;
            continue;
        }
        graph[node].0 = true;

        for &n in graph[node].1.iter() {
            if !graph[n].0 {
                stack.push(n);
            }
        }
    }
    spare_wires
}
```