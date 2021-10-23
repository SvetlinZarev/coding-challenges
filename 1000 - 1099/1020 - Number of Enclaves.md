# [1020. Number of Enclaves](https://leetcode.com/problems/number-of-enclaves/)

## Problem

You are given an `m x n` binary matrix grid, where `0` represents a sea cell
and `1` represents a land cell.

A move consists of walking from one land cell to another adjacent (
4-directionally) land cell or walking off the boundary of the grid.

Return the number of land cells in grid for which we cannot walk off the
boundary of the grid in any number of moves.

#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 500`
* `grid[i][j]` is either `1` or `0`

#### Examples

```text
Input: grid = [[0,0,0,0],[1,0,1,0],[0,1,1,0],[0,0,0,0]]
Output: 3
Explanation: There are three 1s that are enclosed by 0s, and one 1 that is not enclosed because its on the boundary.
```

```text
Input: grid = [[0,1,1,0],[0,0,1,0],[0,0,1,0],[0,0,0,0]]
Output: 0
Explanation: All 1s are either on the boundary or can reach the boundary.
```

## Solution

```rust
const MARKED: i32 = 2;
const LAND: i32 = 1;

pub fn num_enclaves(mut grid: Vec<Vec<i32>>) -> i32 {
    let grid = &mut grid;

    // mark cells connected to the border
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

    grid.iter()
        .skip(1)// skip first row
        .take(grid.len().saturating_sub(2)) // skip last row
        .flat_map(|i| i.iter().skip(1).take(i.len().saturating_sub(2)))
        .filter(|&&x| x == LAND)
        .count() as i32
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

## Related Problems

* [130. Surrounded Regions](/100%20-%20199/130%20-%20Surrounded%20Regions.md)
* [200. Number of Islands](/200%20-%20299/200%20-%20Number%20of%20Islands.md)
* [1254. Number of Closed Islands](/1200%20-%201299/1254%20-%20Number%20of%20Closed%20Islands.md)