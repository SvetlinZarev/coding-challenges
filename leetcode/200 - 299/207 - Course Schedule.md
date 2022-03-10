# [207. Course Schedule](https://leetcode.com/problems/course-schedule/)

## Problem

### Description

There are a total of `numCourses` courses you have to take, labeled from `0`
to `numCourses - 1`. You are given an array `prerequisites`
where `prerequisites[i] = [ai, bi]` indicates that you must take course `bi`
first if you want to take course `ai`.

* For example, the pair `[0, 1]`, indicates that to take course `0` you have to
  first take course `1`.

Return `true` if you can finish all courses. Otherwise, return `false`.

### Constraints

* `1 <= numCourses <= 10^5`
* `0 <= prerequisites.length <= 5000`
* `prerequisites[i].length == 2`
* `0 <= ai, bi < numCourses`
* All the pairs `prerequisites[i]` are unique.

### Examples

```text
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.
```

```text
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0, and to take course 0 you 
should also have finished course 1. So it is impossible.
```

## Solutions

### Cycle detection

**Intuition:** If there is a cycle, (for instance, in order to take course 1,
one has to take course 2, but to take course 2, one has to take course 1) it's
not possible to take all courses

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum Marker {
    Unvisited,
    Undecided,
    Processed,
}

pub fn can_finish(num_courses: usize, prerequisites: Vec<Vec<i32>>) -> bool {
    // build the graph from the given edges
    let mut graph = vec![vec![]; num_courses];
    for edge in prerequisites.into_iter() {
        graph[edge[0] as usize].push(edge[1] as usize);
    }

    // because the graph may not be connected we have to go over all nodes
    let mut visited = vec![Marker::Unvisited; num_courses];
    for node in 0..num_courses {
        if has_cycle(&graph, &mut visited, node) {
            return false;
        }
    }

    true
}

fn has_cycle(graph: &[Vec<usize>], visited: &mut [Marker], node: usize) -> bool {
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
        if has_cycle(graph, visited, next) {
            return true;
        }
    }

    visited[node] = Marker::Processed;
    false
}
```