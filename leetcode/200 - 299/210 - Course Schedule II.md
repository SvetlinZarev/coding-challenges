# [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)

## Problem

### Description

There are a total of `numCourses` courses you have to take, labeled from `0`
to `numCourses - 1`. You are given an array prerequisites
where `prerequisites[i] = [ai, bi]` indicates that you must take course `bi`
first if you want to take course `ai`.

* For example, the pair `[0, 1]`, indicates that to take course `0` you have to
  first take course `1`.

Return the ordering of courses you should take to finish all courses. If there
are many valid answers, return any of them. If it is impossible to finish all
courses, return an empty array.

### Constraints

* `1 <= numCourses <= 2000`
* `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
* `prerequisites[i].length == 2`
* `0 <= ai, bi < numCourses`
* `ai != bi`
* All the pairs `[ai, bi]` are distinct.

### Examples

```text
Input: numCourses = 2, prerequisites = [[1,0]]
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should 
have finished course 0. So the correct course order is [0,1].
```

```text
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should 
have finished both courses 1 and 2. Both courses 1 and 2 should be taken after 
you finished course 0. So one correct course order is [0,1,2,3]. Another correct 
ordering is [0,2,1,3].
```

```text
Input: numCourses = 1, prerequisites = []
Output: [0]
```

## Solutions

### Topological Sort (Kahn's algorithm)

```rust
pub fn find_order(num_courses: usize, prerequisites: Vec<Vec<i32>>) -> Vec<i32> {
    let mut indegree = vec![0u32; num_courses];
    let mut graph = vec![vec![]; num_courses];

    // Edge is (to, from)
    for edge in prerequisites {
        graph[edge[1] as usize].push(edge[0] as usize);
        indegree[edge[0] as usize] += 1;
    }

    let mut queue = vec![];
    for (node, &degree) in indegree.iter().enumerate() {
        if degree == 0 {
            queue.push(node);
        }
    }

    let mut topological_order = vec![];
    while let Some(node) = queue.pop() {
        topological_order.push(node as i32);

        for &neighbour in graph[node].iter() {
            indegree[neighbour] -= 1;
            if indegree[neighbour] == 0 {
                queue.push(neighbour);
            }
        }
    }

    // In case there is a cycle and there is no topological order
    if topological_order.len() != num_courses {
        topological_order.clear();
    }

    topological_order
}
```

### DFS

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum Marker {
    Unvisited,
    Undecided,
    Processed,
}

pub fn find_order(num_courses: usize, prerequisites: Vec<Vec<i32>>) -> Vec<i32> {
    // build the graph from the given edges
    let mut graph = vec![vec![]; num_courses];
    for edge in prerequisites.into_iter() {
        graph[edge[1] as usize].push(edge[0] as usize);
    }

    // because the graph may not be connected we have to go over all nodes
    let mut topological_order = vec![];
    let mut visited = vec![Marker::Unvisited; num_courses];
    for node in 0..num_courses {
        if has_cycle(&graph, &mut visited, &mut topological_order, node) {
            return vec![];
        }
    }

    topological_order.reverse();
    topological_order
}

fn has_cycle(
    graph: &[Vec<usize>],
    visited: &mut [Marker],
    ordered: &mut Vec<i32>,
    node: usize,
) -> bool {
    match visited[node] {
        // We are in a cycle
        Marker::Undecided => return true,
        // We have already processed that subtree and it does not contain a cycle
        Marker::Processed => return false,
        // Check if the current subtree is part of a cycle. We set this state
        // only on unvisited nodes. If we visit them again - then we are in a
        // cycle, otherwise we mark them as "Processed"
        Marker::Unvisited => visited[node] = Marker::Undecided,
    }

    for &next in graph[node].iter() {
        if has_cycle(graph, visited, ordered, next) {
            return true;
        }
    }

    visited[node] = Marker::Processed;
    ordered.push(node as i32);
    false
}
```

## Related Problems

*[207. Course Schedule](207%20-%20Course%20Schedule.md)