# [62. Unique Paths](https://leetcode.com/problems/unique-paths/)

## Problem

A robot is located at the top-left corner (`[0][0]`) of a `m x n` grid.

The robot can only move either down or right at any point in time. The robot is
trying to reach the bottom-right corner (`[m-1][n-1]`) of the grid.

How many possible unique paths are there?

#### Constraints

* `1 <= m, n <= 100`
* It's guaranteed that the answer will be less than or equal to `2 * 10^9`.

#### Examples

```text
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Down -> Down
2. Down -> Down -> Right
3. Down -> Right -> Down
```

```text
Input: m = 7, n = 3
Output: 28
```

```text
Input: m = 3, n = 3
Output: 6
```

## Solution

```rust
pub fn unique_paths(m: i32, n: i32) -> i32 {
    let mut grid = vec![vec![0; n as usize]; m as usize];
    grid[0][0] = 1;

    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if r > 0 {
                grid[r][c] += grid[r - 1][c];
            }

            if c > 0 {
                grid[r][c] += grid[r][c - 1];
            }
        }
    }

    grid[(m - 1) as usize][(n - 1) as usize]
}
```

## Related problems

* [63. Unique Paths II](63%20-%20Unique%20Paths%20II.md)
* [980. Unique Paths III](/leetcode/900%20-%20999/980%20-%20Unique%20Paths%20III.md)