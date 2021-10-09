# [547. Number of Provinces](https://leetcode.com/problems/number-of-provinces/)

## Problem

There are `n` cities. Some of them are connected, while some are not. If
city `a` is connected directly with city `b`, and city `b` is connected directly
with city `c`, then city `a` is connected indirectly with city `c`.

A province is a group of directly or indirectly connected cities and no other
cities outside of the group.

You are given an `n x n` matrix `isConnected` where `isConnected[i][j] = 1` if
the ith city and the jth city are directly connected,
and `isConnected[i][j] = 0` otherwise.

Return the total number of provinces.

#### Constraints

* `1 <= n <= 200`
* `n == isConnected.length`
* `n == isConnected[i].length`
* `isConnected[i][j] is 1 or 0`
* `isConnected[i][i] == 1`
* `isConnected[i][j] == isConnected[j][i]`

#### Examples

```text
Input: isConnected = [[1,1,0],[1,1,0],[0,0,1]]
Output: 2
```

```text
Input: isConnected = [[1,0,0],[0,1,0],[0,0,1]]
Output: 3
```

## Solution

```rust
pub fn find_circle_num(mut grid: Vec<Vec<i32>>) -> i32 {
    let mut provinces = 0;
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == 1 && dfs(r, c, &mut grid) {
                provinces += 1;
            }
        }
    }
    provinces
}

fn dfs(r: usize, c: usize, grid: &mut Vec<Vec<i32>>) -> bool {
    if grid[r][c] == 0 {
        return false;
    }

    grid[r][c] = 0;
    grid[c][r] = 0;

    for col in 0..grid[r].len() {
        if grid[col][r] == 1 {
            dfs(col, r, grid);
        }
    }

    true
}
```