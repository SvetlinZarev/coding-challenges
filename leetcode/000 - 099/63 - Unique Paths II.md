# [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

## Problem

A robot is located at the top-left corner of a `m x n` grid.

The robot can only move either down or right at any point in time. The robot is
trying to reach the bottom-right corner of the grid.

Now consider if some obstacles are added to the grids. How many unique paths
would there be?

An obstacle and space is marked as `1` and `0` respectively in the grid.

#### Constraints

* `m == obstacleGrid.length`
* `n == obstacleGrid[i].length`
* `1 <= m, n <= 100`
* `obstacleGrid[i][j]` is `0` or `1`

#### Examples

```text
Input: obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
Output: 2
Explanation: There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

```text
Input: obstacleGrid = [[0,1],[0,0]]
Output: 1
```

## Solution

```rust
pub fn unique_paths_with_obstacles(grid: Vec<Vec<i32>>) -> i32 {
    let mut dp = vec![vec![0; grid[0].len()]; grid.len()];

    // Normally, [0][0] would have been 1, but there is this (IMO) wrong corner
    // case where the grid is just 1 cell  which is an obstacle (`[[1]]`).
    // In that  case LeetCode expects `0` as an answer. So if the starting cell
    // is an obstacle (i.e. `1`), make the first cell a 0, thus producing a 0 result.
    dp[0][0] = grid[0][0] ^ 1;

    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == 0 {
                if c > 0 {
                    dp[r][c] += dp[r][c - 1];
                }
                if r > 0 {
                    dp[r][c] += dp[r - 1][c];
                }
            }
        }
    }

    dp[dp.len() - 1][dp[0].len() - 1]
}
```

## Related problems

* [62. Unique Paths](62%20-%20Unique%20Paths.md)
* [980. Unique Paths III](/leetcode/900%20-%20999/980%20-%20Unique%20Paths%20III.md)