# [934. Shortest Bridge](https://leetcode.com/problems/shortest-bridge/)

## Problem

### Description

You are given an `n x n` binary matrix grid where `1` represents land and `0`
represents water.

An island is a 4-directionally connected group of `1`'s not connected to any
other `1`'s. There are exactly two islands in grid.

You may change `0`'s to `1`'s to connect the two islands to form one island.

Return the smallest number of `0`'s you must flip to connect the two islands.

### Constraints

* `n == grid.length == grid[i].length`
* `2 <= n <= 100`
* `grid[i][j]` is either `0` or `1`.
* There are exactly two islands in `grid`.

### Examples

```text
Input: grid = [[0,1],[1,0]]
Output: 1
```

```text
Input: grid = [[0,1,0],[0,0,0],[0,0,1]]
Output: 2
```

```text
Input: grid = [[1,1,1,1,1],[1,0,0,0,1],[1,0,1,0,1],[1,0,0,0,1],[1,1,1,1,1]]
Output: 1
```

## Solutions

### Bidirectional BFS

Use DFS to find out the border regions (i.e. those land cells that are touching
water cells) of each island and put them in a queue per island.

Then run a bidirectional search from those two queues:

```rust
use std::collections::HashSet;

const WATER: i32 = 0;
const LAND: i32 = 1;
const MARKED_LAND: i32 = 2;
const MARKED_WATER: i32 = 3;

pub fn shortest_bridge(mut grid: Vec<Vec<i32>>) -> i32 {
    let mut left = HashSet::new();
    let mut right = HashSet::new();
    let mut next = HashSet::new();

    //mark the first island
    'l: for r in 0..grid.len() {
        for c in 0..grid[0].len() {
            if grid[r][c] == LAND {
                mark_and_enqueue(&mut grid, &mut left, r, c);
                break 'l;
            }
        }
    }

    //mark the second island
    'r: for r in 0..grid.len() {
        for c in 0..grid[0].len() {
            if grid[r][c] == LAND {
                mark_and_enqueue(&mut grid, &mut right, r, c);
                break 'r;
            }
        }
    }

    let mut len = 0;
    while !left.is_empty() && !right.is_empty() {
        if left.len() > right.len() {
            std::mem::swap(&mut left, &mut right);
        }

        for (r, c) in left.drain() {
            if r > 0 { // top
                if right.contains(&(r - 1, c)) {
                    return len;
                }
                if grid[r - 1][c] == WATER {
                    grid[r - 1][c] = MARKED_WATER;
                    next.insert((r - 1, c));
                }
            }

            if c > 0 { //left
                if right.contains(&(r, c - 1)) {
                    return len;
                }
                if grid[r][c - 1] == WATER {
                    grid[r][c - 1] = MARKED_WATER;
                    next.insert((r, c - 1));
                }
            }

            if c < grid[r].len() - 1 { //right
                if right.contains(&(r, c + 1)) {
                    return len;
                }
                if grid[r][c + 1] == WATER {
                    grid[r][c + 1] = MARKED_WATER;
                    next.insert((r, c + 1));
                }
            }

            if r < grid.len() - 1 { //down
                if right.contains(&(r + 1, c)) {
                    return len;
                }
                if grid[r + 1][c] == WATER {
                    grid[r + 1][c] = MARKED_WATER;
                    next.insert((r + 1, c));
                }
            }
        }

        std::mem::swap(&mut left, &mut next);
        len += 1
    }

    -1
}

fn mark_and_enqueue(
    grid: &mut Vec<Vec<i32>>,
    queue: &mut HashSet<(usize, usize)>,
    r: usize,
    c: usize,
) {
    if grid[r][c] != LAND {
        return;
    }
    grid[r][c] = MARKED_LAND;

    if r > 0 { //top
        if grid[r - 1][c] == WATER {
            queue.insert((r, c));
        } else if grid[r - 1][c] == LAND {
            mark_and_enqueue(grid, queue, r - 1, c);
        }
    }

    if c > 0 { //left
        if grid[r][c - 1] == WATER {
            queue.insert((r, c));
        } else if grid[r][c - 1] == LAND {
            mark_and_enqueue(grid, queue, r, c - 1);
        }
    }

    if c < grid[r].len() - 1 { //right
        if grid[r][c + 1] == WATER {
            queue.insert((r, c));
        } else if grid[r][c + 1] == LAND {
            mark_and_enqueue(grid, queue, r, c + 1);
        }
    }

    if r < grid.len() - 1 { // down
        if grid[r + 1][c] == WATER {
            queue.insert((r, c));
        } else if grid[r + 1][c] == LAND {
            mark_and_enqueue(grid, queue, r + 1, c);
        }
    }
}
```