# [1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)

## Problem

Given an `n x n` binary matrix grid, return the length of the shortest clear
path in the matrix. If there is no clear path, return `-1`.

A clear path in a binary matrix is a path from the top-left cell (
i.e., `(0, 0)`) to the bottom-right cell (i.e., `(n - 1, n - 1)`) such that:

* All the visited cells of the path are `0`.
* All the adjacent cells of the path are 8-directionally connected (i.e., they
  are different and they share an edge or a corner).

The length of a clear path is the number of visited cells of this path.

#### Constraints

* `n == grid.length`
* `n == grid[i].length`
* `1 <= n <= 100`
* `grid[i][j]` is `0` or `1`

#### Examples

```text
Input: grid = [[0,1],[1,0]]
Output: 2
```

```text
Input: grid = [[0,0,0],[1,1,0],[1,1,0]]
Output: 4
```

```text
Input: grid = [[1,0,0],[1,1,0],[1,1,0]]
Output: -1
```

## Solution

The easiest solution is to use the Dijkstra algorithm. It's essentially the same
as A*, but with a heuristic that always returns 0.

In order for A* to work we have to choose a proper heuristic. On the
[Stanford](http://theory.stanford.edu/~amitp/GameProgramming/Heuristics.html#heuristics-for-grid-maps)
website, for 8-directionally connected cells it's recommended to use "Diagonal
Distance":

* On a square grid that allows 4 directions of movement, use Manhattan
  distance (L1).
* On a square grid that allows 8 directions of movement, use Diagonal distance (
  L∞).
* On a square grid that allows any direction of movement, you might or might not
  want Euclidean distance (L2). If A* is finding paths on the grid but you are
  allowing movement not on the grid, you may want to consider other
  representations of the map.
* On a hexagon grid that allows 6 directions of movement, use Manhattan distance
  adapted to hexagonal grids.

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

const N: &[(isize, isize); 8] = &[
    (-1, -1),
    (-1, 0),
    (-1, 1),
    (0, -1),
    (0, 1),
    (1, -1),
    (1, 0),
    (1, 1),
];

pub fn shortest_path_binary_matrix(mut grid: Vec<Vec<i32>>) -> i32 {
    if grid[0][0] != 0 {
        return -1;
    }

    let mut pq = BinaryHeap::new();
    pq.push((Reverse((heuristic(&grid, 0, 0) + 1, 1i32)), (0, 0)));

    while let Some((Reverse((_, s)), (r, c))) = pq.pop() {
        if grid[r][c] != 0 {
            continue;
        }
        grid[r][c] = 255;

        if r == grid.len() - 1 && c == grid[0].len() - 1 {
            return s;
        }

        for (dr, dc) in N.iter().copied() {
            let rr = (r as isize) + dr;
            if rr < 0 || rr as usize >= grid.len() {
                continue;
            }

            let cc = (c as isize) + dc;
            if cc < 0 || cc as usize >= grid[0].len() {
                continue;
            }

            let rr = rr as usize;
            let cc = cc as usize;
            if grid[rr][cc] != 0 {
                continue;
            }

            let m = heuristic(&grid, rr, cc);
            let s = s + 1;
            pq.push((Reverse((m + s, s)), (rr, cc)));
        }
    }

    -1
}

fn heuristic(grid: &Vec<Vec<i32>>, r: usize, c: usize) -> i32 {
    const D1: isize = 1;
    const D2: isize = 1;

    let dx = (grid.len() - r - 1) as isize;
    let dy = (grid[0].len() - c - 1) as isize;

    (D1 * (dx + dy) + (D2 - 2 * D1) * dx.min(dy)) as i32
}

```