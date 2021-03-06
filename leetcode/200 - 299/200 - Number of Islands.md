# [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

## Problem

### Description

Given an `m x n` 2D binary grid `grid` which represents a map of `1`s (land)
and `0`s (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands
horizontally or vertically. You may assume all four edges of the grid are all
surrounded by water.

### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 300`
* `grid[i][j] is '0' or '1'`

### Examples

```text
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

```text
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

## Solutions

### Using a bfs/flood-fill approach

If we are on a land:

* `push_back()` the neighbouring land, as it's part of the current island
* `push_front()` the neighbouring water, in order to act as a separator

If we re on water:

* `push_front()` the neighbouring water, in order to act as a separator
* `push_front()` the current tile then `push_front()` the neighbouring land

We need to separate the land tiles with water tiles in the queue, because we use
the transition from water to land to count the number of islands. So when we
push back the neighbouring land, the land tiles are not separated, so we do not
increase the islands counter

We mark the visited tiles in place, but mark the land/water tiles differently
because we still need them to decide whether to increase the counter

* Runtime: 12 ms
* Memory Usage: 9.3 MB

```rust
use std::collections::VecDeque;

pub fn num_islands(mut grid: Vec<Vec<char>>) -> i32 {
    let mut islands = 0;
    let mut queue = VecDeque::with_capacity(32);
    queue.push_back((0, 0));

    let mut was_water = true;
    while let Some((r, c)) = queue.pop_back() {
        match grid[r][c] {
            '0' => was_water = true,
            '1' => {
                if was_water {
                    islands += 1;
                    was_water = false;
                }
            }
            'X' => {
                was_water = false;
                continue;
            }
            _ => {
                was_water = true;
                continue;
            }
        }

        //left
        if c > 0 {
            match grid[r][c - 1] {
                '0' => queue.push_front((r, c - 1)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r, c - 1));
                    } else {
                        queue.push_back((r, c - 1));
                    }
                }
                _ => {}
            }
        }

        // center
        grid[r][c] = if was_water { 'O' } else { 'X' };

        //right
        if c < grid[r].len() - 1 {
            match grid[r][c + 1] {
                '0' => queue.push_front((r, c + 1)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r, c + 1));
                    } else {
                        queue.push_back((r, c + 1));
                    }
                }
                _ => {}
            }
        }

        //top
        if r > 0 {
            match grid[r - 1][c] {
                '0' => queue.push_front((r - 1, c)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r - 1, c));
                    } else {
                        queue.push_back((r - 1, c));
                    }
                }
                _ => {}
            }
        }

        //bottom
        if r < grid.len() - 1 {
            match grid[r + 1][c] {
                '0' => queue.push_front((r + 1, c)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r + 1, c));
                    } else {
                        queue.push_back((r + 1, c));
                    }
                }
                _ => {}
            }
        }
    }

    islands
}
```

### Simplified Solution

```rust
const SEA: char = '0';
const LAND: char = '1';

pub fn num_islands(mut grid: Vec<Vec<char>>) -> i32 {
    let mut islands = 0;
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == LAND {
                islands += 1;
                mark_connected(&mut grid, r, c);
            }
        }
    }

    islands
}

fn mark_connected(grid: &mut Vec<Vec<char>>, r: usize, c: usize) {
    let mut stack = vec![];
    stack.push((r, c));

    while let Some((r, c)) = stack.pop() {
        if grid[r][c] != LAND {
            continue;
        }
        grid[r][c] = SEA;

        if r > 0 && grid[r - 1][c] == LAND {
            stack.push((r - 1, c));
        }

        if c > 0 && grid[r][c - 1] == LAND {
            stack.push((r, c - 1));
        }

        if c < grid[r].len() - 1 && grid[r][c + 1] == LAND {
            stack.push((r, c + 1));
        }

        if r < grid.len() - 1 && grid[r + 1][c] == LAND {
            stack.push((r + 1, c));
        }
    }
}
```

## Related problems

* [130. Surrounded Regions](/leetcode/100%20-%20199/130%20-%20Surrounded%20Regions.md)
* [417. Pacific Atlantic Water Flow](/leetcode/400%20-%20499/417%20-%20Pacific%20Atlantic%20Water%20Flow.md)
* [695. Max Area of Island](/leetcode/600%20-%20699/695%20-%20Max%20Area%20of%20Island.md)
* [1020. Number of Enclaves](/leetcode/1000%20-%201099/1020%20-%20Number%20of%20Enclaves.md)
* [1254. Number of Closed Islands](/leetcode/1200%20-%201299/1254%20-%20Number%20of%20Closed%20Islands.md)
* [1905. Count Sub Islands](/leetcode/1900%20-%201999/1905%20-%20Count%20Sub%20Islands.md)