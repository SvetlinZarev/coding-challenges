# [695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/)

## Problem

You are given an `m x n` binary matrix `grid`. An island is a group of 1's (
representing land) connected 4-directionally (horizontal or vertical.) You may
assume all four edges of the grid are surrounded by water.

The area of an island is the number of cells with a value `1` in the island.

Return the maximum area of an island in `grid`. If there is no island,
return `0`.

#### Example

```text
Input: grid = [
  [0,0,1,0,0,0,0,1,0,0,0,0,0],
  [0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,1,1,0,1,0,0,0,0,0,0,0,0],
  [0,1,0,0,1,1,0,0,1,0,1,0,0],
  [0,1,0,0,1,1,0,0,1,1,1,0,0],
  [0,0,0,0,0,0,0,0,0,0,1,0,0],
  [0,0,0,0,0,0,0,1,1,1,0,0,0],
  [0,0,0,0,0,0,0,1,1,0,0,0,0]
]
Output: 6
Explanation: The answer is not 11, because the island must be connected 4-directionally.
```

#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 50`
* `grid[i][j] is either 0 or 1`

## Solution

```rust
use std::collections::VecDeque;

pub fn max_area_of_island(grid: Vec<Vec<i32>>) -> i32 {
    if grid.is_empty() {
        return 0;
    }

    let mut visited = vec![0u8; grid.len() * grid[0].len()];
    let mut queue = VecDeque::with_capacity(64);
    queue.push_back((0, 0));

    let mut best = 0;
    let mut land = 0;
    while let Some((r, c)) = queue.pop_back() {
        let currently_on_land = grid[r][c] != 0;
        let pos = pos(r, c, grid[0].len());

        if visited[pos] != 0 {
            if !currently_on_land {
                best = best.max(land);
                land = 0;
            }
            continue;
        }

        visited[pos] = 1;

        if currently_on_land {
            land += 1;
        } else {
            best = best.max(land);
            land = 0;
        }

        //left
        if c > 0 {
            if grid[r][c - 1] == 0 {
                queue.push_front((r, c - 1));
            } else {
                if currently_on_land {
                    queue.push_back((r, c - 1));
                } else {
                    queue.push_front((r, c));
                    queue.push_front((r, c - 1));
                }
            }
        }

        //right
        if c < grid[0].len() - 1 {
            if grid[r][c + 1] == 0 {
                queue.push_front((r, c + 1));
            } else {
                if currently_on_land {
                    queue.push_back((r, c + 1));
                } else {
                    queue.push_front((r, c));
                    queue.push_front((r, c + 1));
                }
            }
        }

        //top
        if r > 0 {
            if grid[r - 1][c] == 0 {
                queue.push_front((r - 1, c));
            } else {
                if currently_on_land {
                    queue.push_back((r - 1, c));
                } else {
                    queue.push_front((r, c));
                    queue.push_front((r - 1, c));
                }
            }
        }

        //bottom
        if r < grid.len() - 1 {
            if grid[r + 1][c] == 0 {
                queue.push_front((r + 1, c));
            } else {
                if currently_on_land {
                    queue.push_back((r + 1, c));
                } else {
                    queue.push_front((r, c));
                    queue.push_front((r + 1, c));
                }
            }
        }
    }

    best.max(land)
}


fn pos(r: usize, c: usize, side: usize) -> usize {
    r * side + c
}
```
