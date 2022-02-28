# [1162. As Far from Land as Possible](https://leetcode.com/problems/as-far-from-land-as-possible/)

## Problem

### Description

Given an `n x n` grid containing only values `0` and `1`, where `0` represents
water and `1` represents land, find a water cell such that its distance to the
nearest land cell is maximized, and return the distance. If no land or water
exists in the grid, return `-1`.

The distance used in this problem is the Manhattan distance: the distance
between two cells `(x0, y0)` and `(x1, y1)` is `|x0 - x1| + |y0 - y1|`.

### Constraints

* `n == grid.length`
* `n == grid[i].length`
* `1 <= n <= 100`
* `grid[i][j]` is `0` or `1`

### Examples

```text
Input: grid = [
    [1,0,1],
    [0,0,0],
    [1,0,1]
]
Output: 2
Explanation: The cell (1, 1) is as far as possible from all the land with distance 2.
```

```text
Input: grid = [
    [1,0,0],
    [0,0,0],
    [0,0,0]
]
Output: 4
Explanation: The cell (2, 2) is as far as possible from all the land with distance 4.
```

## Solutions

### DP

```rust

const WATER: i32 = 0;
const LAND: i32 = 1;

pub fn max_distance(grid: Vec<Vec<i32>>) -> i32 {
    let mut dist = vec![vec![i32::MAX; grid[0].len()]; grid.len()];

    // top => bottom, left => right
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            match grid[r][c] {
                LAND => dist[r][c] = 0,
                WATER => {
                    if r > 0 && dist[r - 1][c] != i32::MAX {
                        dist[r][c] = dist[r][c].min(dist[r - 1][c] + 1);
                    }

                    if c > 0 && dist[r][c - 1] != i32::MAX {
                        dist[r][c] = dist[r][c].min(dist[r][c - 1] + 1);
                    }
                }
                _ => unreachable!(),
            }
        }
    }

    // bottom => top, right => left
    for r in (0..grid.len()).rev() {
        for c in (0..grid[r].len()).rev() {
            match grid[r][c] {
                LAND => dist[r][c] = 0,
                WATER => {
                    if r < grid.len() - 1 && dist[r + 1][c] != i32::MAX {
                        dist[r][c] = dist[r][c].min(dist[r + 1][c] + 1);
                    }

                    if c < grid[r].len() - 1 && dist[r][c + 1] != i32::MAX {
                        dist[r][c] = dist[r][c].min(dist[r][c + 1] + 1);
                    }
                }
                _ => unreachable!(),
            }
        }
    }

    // for row in dist.iter() {
    //     println!("{:?}", row);
    // }

    dist.into_iter()
        .flat_map(|x| x.into_iter())
        .max()
        .map(|x| match x {
            0 | i32::MAX => -1,
            x => x,
        })
        .unwrap()
}
```

### BFS

```rust
use std::collections::VecDeque;

//const WATER: i32 = 0;
const LAND: i32 = 1;

pub fn max_distance(grid: Vec<Vec<i32>>) -> i32 {
    let mut queue = VecDeque::new();
    let mut dist = vec![vec![i32::MAX; grid[0].len()]; grid.len()];

    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == LAND {
                queue.push_back(((r, c), 0));
                dist[r][c] = 0;
            }
        }
    }

    // There is either no land or water in the grid
    if queue.is_empty() || queue.len() == grid.len() * grid[0].len() {
        return -1;
    }

    let mut answer = -1;
    while let Some(((r, c), d)) = queue.pop_front() {
        // avoid infinite loops:
        // * If the value in `dist` is lower or equal, then we've found a 
        //   closer "land", so no need to check this branch
        // * check for 0, (i.e. land, because the dist for land == 0)
        if dist[r][c] <= d && d != 0 {
            continue;
        }

        dist[r][c] = d;
        answer = answer.max(d);

        if r > 0 && dist[r - 1][c] > d + 1 {
            queue.push_back(((r - 1, c), d + 1));
        }

        if c > 0 && dist[r][c - 1] > d + 1 {
            queue.push_back(((r, c - 1), d + 1));
        }

        if c < grid[r].len() - 1 && dist[r][c + 1] > d + 1 {
            queue.push_back(((r, c + 1), d + 1));
        }

        if r < grid.len() - 1 && dist[r + 1][c] > d + 1 {
            queue.push_back(((r + 1, c), d + 1));
        }
    }

    answer
}
```