# [1254. Number of Closed Islands](https://leetcode.com/problems/number-of-closed-islands/)

## Problem

Given a 2D grid consists of `0`s (land) and `1`s (water). An island is a maximal
4-directionally connected group of 0s and a closed island is an island totally (
all left, top, right, bottom) surrounded by `1`s.

Return the number of closed islands.

#### Constraints

* `1 <= grid.length, grid[0].length <= 100`
* `0 <= grid[i][j] <=1`

#### Examples

```text
Input: grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
Output: 2
Explanation: 
Islands in gray are closed because they are completely surrounded by water (group of 1s).
```

```text
Input: grid = [[0,0,1,0,0],[0,1,0,1,0],[0,1,1,1,0]]
Output: 1
```

```text
Input: grid = [[1,1,1,1,1,1,1],
               [1,0,0,0,0,0,1],
               [1,0,1,1,1,0,1],
               [1,0,1,0,1,0,1],
               [1,0,1,1,1,0,1],
               [1,0,0,0,0,0,1],
               [1,1,1,1,1,1,1]]
Output: 2
```

## Solution

```rust
const MARKED: i32 = 2;
const LAND: i32 = 0;

pub fn closed_island(mut grid: Vec<Vec<i32>>) -> i32 {
    let grid = &mut grid;

    // mark any cells connected to the border
    for col in 0..grid[0].len() {
        if grid[0][col] == LAND {
            mark_connected_cells(grid, 0, col);
        }
    }
    for col in 0..grid[grid.len() - 1].len() {
        if grid[grid.len() - 1][col] == LAND {
            mark_connected_cells(grid, grid.len() - 1, col);
        }
    }
    for row in 1..grid.len() - 1 {
        if grid[row][0] == LAND {
            mark_connected_cells(grid, row, 0);
        }

        if grid[row][grid[row].len() - 1] == LAND {
            mark_connected_cells(grid, row, grid[row].len() - 1);
        }
    }

    // Now mark any "closed islands" and increment the counter on any
    // unmarked cell. Then mark the whole island via DFS, so that we don't 
    // count its cells multiple times
    let mut closed_islands = 0;
    for r in 1..grid.len() - 1 {
        for c in 1..grid[r].len() - 1 {
            if grid[r][c] == LAND {
                closed_islands += 1;
                mark_connected_cells(grid, r, c);
            }
        }
    }
    closed_islands
}

fn mark_connected_cells(grid: &mut Vec<Vec<i32>>, r: usize, c: usize) {
    if grid[r][c] != LAND {
        return;
    }

    grid[r][c] = MARKED;

    if r > 0 {
        mark_connected_cells(grid, r - 1, c);
    }
    if c > 0 {
        mark_connected_cells(grid, r, c - 1);
    }
    if c < grid[r].len() - 1 {
        mark_connected_cells(grid, r, c + 1);
    }
    if r < grid.len() - 1 {
        mark_connected_cells(grid, r + 1, c);
    }
}
```

## Related problems

* [130. Surrounded Regions](/leetcode/100%20-%20199/130%20-%20Surrounded%20Regions.md)
* [200. Number of Islands](/leetcode/200%20-%20299/200%20-%20Number%20of%20Islands.md)
* [417. Pacific Atlantic Water Flow](/leetcode/400%20-%20499/417%20-%20Pacific%20Atlantic%20Water%20Flow.md)
* [1020. Number of Enclaves](/leetcode/1000%20-%201099/1020%20-%20Number%20of%20Enclaves.md)
