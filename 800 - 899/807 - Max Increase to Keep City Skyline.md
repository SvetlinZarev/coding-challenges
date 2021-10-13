# [807. Max Increase to Keep City Skyline](https://leetcode.com/problems/max-increase-to-keep-city-skyline/)

## Problem

There is a city composed of `n x n` blocks, where each block contains a single
building shaped like a vertical square prism. You are given a 0-indexed `n x n`
integer matrix grid where `grid[r][c]` represents the height of the building
located in the block at row `r` and column `c`.

A city's skyline is the outer contour formed by all the building when viewing
the side of the city from a distance. The skyline from each cardinal direction
north, east, south, and west may be different.

We are allowed to increase the height of any number of buildings by any amount (
the amount can be different per building). The height of a 0-height building can
also be increased. However, increasing the height of a building should not
affect the city's skyline from any cardinal direction.

Return the maximum total sum that the height of the buildings can be increased
by without changing the city's skyline from any cardinal direction.

#### Constraints

* `2 <= n <= 50`
* `n == grid.length`
* `n == grid[r].length`
* `0 <= grid[r][c] <= 100`

#### Examples

```text
Input: grid = [[3,0,8,4],[2,4,5,7],[9,2,6,3],[0,3,1,0]]
Output: 35
Explanation: The building heights are shown in the center of the above image.
The skylines when viewed from each cardinal direction are drawn in red.
The grid after increasing the height of buildings without affecting skylines is:
gridNew = [ [8, 4, 8, 7],
            [7, 4, 7, 7],
            [9, 4, 8, 7],
            [3, 3, 3, 3] ]
```

```text
Input: grid = [[0,0,0],[0,0,0],[0,0,0]]
Output: 0
Explanation: Increasing the height of any building will result in the skyline changing.
```

## Solution

```rust
pub fn max_increase_keeping_skyline(grid: Vec<Vec<i32>>) -> i32 {
    let mut vertical = vec![0; grid.len()];
    let mut horizontal = vec![0; grid[0].len()];

    for r in 0..grid.len() {
        vertical[r] = grid[r].iter().copied().max().unwrap_or(0);
    }

    for c in 0..grid[0].len() {
        for r in 0..grid.len() {
            horizontal[c] = horizontal[c].max(grid[r][c]);
        }
    }

    let mut increase = 0;

    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            increase += vertical[r].min(horizontal[c]) - grid[r][c];
        }
    }

    increase
}
```