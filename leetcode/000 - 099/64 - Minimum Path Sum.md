# [64. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

## Problem

Given a `m x n` grid filled with non-negative numbers, find a path from top left
to bottom right, which minimizes the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 200`
* `0 <= grid[i][j] <= 100`

#### Examples

```text
Input: grid = [[1,3,1],[1,5,1],[4,2,1]]
Output: 7
Explanation: Because the path 1 → 3 → 1 → 1 → 1 minimizes the sum.
```

```text
Input: grid = [[1,2,3],[4,5,6]]
Output: 12
```

## Solution

```rust
pub fn min_path_sum(grid: Vec<Vec<i32>>) -> i32 {
    let mut dp = vec![vec![0; grid[0].len()]; grid.len()];

    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            let mut up = i32::MAX;
            let mut left = i32::MAX;

            if r > 0 {
                up = grid[r][c] + dp[r - 1][c];
            }

            if c > 0 {
                left = grid[r][c] + dp[r][c - 1];
            }

            if c > 0 || r > 0 {
                dp[r][c] = up.min(left);
            } else {
                dp[r][c] = grid[r][c];
            }
        }
    }

    dp[dp.len() - 1][dp[0].len() - 1]
}
```

Or solving it without additional memory:

```rust
pub fn min_path_sum(mut grid: Vec<Vec<i32>>) -> i32 {
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            let mut up = i32::MAX;
            let mut left = i32::MAX;

            if r > 0 {
                up = grid[r - 1][c];
            }

            if c > 0 {
                left = grid[r][c - 1];
            }

            if c > 0 || r > 0 {
                grid[r][c] += up.min(left);
            }
        }
    }

    grid[grid.len() - 1][grid[0].len() - 1]
}
```

And we can remove the `if`-checks

```rust
pub fn min_path_sum(mut grid: Vec<Vec<i32>>) -> i32 {
    // The first row is special, because it does not have
    // upper cells, so we can skip the checks for that
    for c in 1..grid[0].len() {
        grid[0][c] += grid[0][c - 1];
    }

    // The first column is special , because it does not have
    // cells to the left, so we can skip those checks
    for r in 1..grid.len() {
        grid[r][0] += grid[r - 1][0];
    }

    // handle the rest
    for r in 1..grid.len() {
        for c in 1..grid[r].len() {
            grid[r][c] += grid[r - 1][c].min(grid[r][c - 1]);
        }
    }

    grid[grid.len() - 1][grid[0].len() - 1]
}
```