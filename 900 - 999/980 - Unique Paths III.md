# [980. Unique Paths III](https://leetcode.com/problems/unique-paths-iii/)

## Problem

You are given an m x n integer array grid where `grid[i][j]` could be:

* `1` representing the starting square. There is exactly one starting square.
* `2` representing the ending square. There is exactly one ending square.
* `0` representing empty squares we can walk over.
* `-1` representing obstacles that we cannot walk over.

Return the number of 4-directional walks from the starting square to the ending
square, that walk over every non-obstacle square exactly once.

#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 20`
* `1 <= m * n <= 20`
* `-1 <= grid[i][j] <= 2`
* There is exactly one starting cell and one ending cell.

#### Examples

```text
Input: grid = [[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
Output: 2
Explanation: We have the following two paths: 
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2)
2. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2)
```

```text
Input: grid = [[1,0,0,0],[0,0,0,0],[0,0,0,2]]
Output: 4
Explanation: We have the following four paths: 
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2),(2,3)
2. (0,0),(0,1),(1,1),(1,0),(2,0),(2,1),(2,2),(1,2),(0,2),(0,3),(1,3),(2,3)
3. (0,0),(1,0),(2,0),(2,1),(2,2),(1,2),(1,1),(0,1),(0,2),(0,3),(1,3),(2,3)
4. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2),(2,3)
```

```text
Input: grid = [[0,1],[2,0]]
Output: 0
Explanation: There is no path that walks over every empty square exactly once.
Note that the starting and ending square can be anywhere in the grid.
```

## Solution

### DFS

```rust
pub fn unique_paths_iii(mut grid: Vec<Vec<i32>>) -> i32 {
    let mut r = 0;
    let mut c = 0;
    let mut blocked = 0;

    // find the starting cell and count the number of cells we cannot walk over
    for row in 0..grid.len() {
        for col in 0..grid[row].len() {
            if grid[row][col] == 1 {
                r = row;
                c = col;
            } else if grid[row][col] == -1 {
                blocked += 1;
            }
        }
    }

    let free_cells = grid.len() * grid[0].len() - blocked;
    let mut paths = 0;

    dfs(&mut grid, &mut paths, r, c, free_cells - 1);

    paths
}

fn dfs(grid: &mut Vec<Vec<i32>>, paths: &mut i32, r: usize, c: usize, cells: usize) {
    let current = grid[r][c];

    if current == 2 {
        // We've reached the destination, check if we have used all cells
        if cells == 0 {
            *paths += 1;
        }
        return;
    }

    grid[r][c] = -1;

    if r > 0 && grid[r - 1][c] != -1 {
        dfs(grid, paths, r - 1, c, cells - 1);
    }
    if c > 0 && grid[r][c - 1] != -1 {
        dfs(grid, paths, r, c - 1, cells - 1);
    }
    if c < grid[r].len() - 1 && grid[r][c + 1] != -1 {
        dfs(grid, paths, r, c + 1, cells - 1);
    }
    if r < grid.len() - 1 && grid[r + 1][c] != -1 {
        dfs(grid, paths, r + 1, c, cells - 1);
    }

    grid[r][c] = current;
}
```

## Related Problems

* [62. Unique Paths](/000%20-%20099/62%20-%20Unique%20Paths.md)
* [63. Unique Paths II](/000%20-%20099/63%20-%20Unique%20Paths%20II.md)
