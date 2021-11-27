# [542. 01 Matrix](https://leetcode.com/problems/01-matrix/)

## Problem

Given an `m x n` binary matrix `mat`, return the distance of the nearest `0` for
each cell.

The distance between two adjacent cells is 1.

#### Constraints

* `m == mat.length`
* `n == mat[i].length`
* `1 <= m, n <= 10^4`
* `1 <= m * n <= 10^4`
* `mat[i][j]` is either `0` or `1`.
* There is at least one `0` in mat.

#### Examples

```text
Input: mat = [[0,0,0],[0,1,0],[0,0,0]]
Output: [[0,0,0],[0,1,0],[0,0,0]]
```

```text
Input: mat = [[0,0,0],[0,1,0],[1,1,1]]
Output: [[0,0,0],[0,1,0],[1,2,1]]
```

## Solution

```rust
const FILLER: i32 = 10000 * 10000 + 1;

pub fn update_matrix(mat: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    let mut dist = vec![vec![FILLER; mat[0].len()]; mat.len()];

    for r in 0..mat.len() {
        for c in 0..mat[r].len() {
            move_right_down(r, c, &mat, &mut dist);
        }
    }

    for r in (0..mat.len()).rev() {
        for c in (0..mat[r].len()).rev() {
            move_left_up(r, c, &mat, &mut dist);
        }
    }

    dist
}

fn move_right_down(r: usize, c: usize, mat: &Vec<Vec<i32>>, dist: &mut Vec<Vec<i32>>) {
    if mat[r][c] == 0 {
        dist[r][c] = 0;
    } else {
        let mut min = dist[r][c];

        if c > 0 {
            min = min.min(dist[r][c - 1] + 1);
        }

        if r > 0 {
            min = min.min(dist[r - 1][c] + 1);
        }

        dist[r][c] = min;
    }
}

fn move_left_up(r: usize, c: usize, mat: &Vec<Vec<i32>>, dist: &mut Vec<Vec<i32>>) {
    if mat[r][c] == 0 {
        dist[r][c] = 0;
    } else {
        let mut min = dist[r][c];

        if c < mat[r].len() - 1 {
            min = min.min(dist[r][c + 1] + 1);
        }

        if r < mat.len() - 1 {
            min = min.min(dist[r + 1][c] + 1);
        }

        dist[r][c] = min;
    }
}
```