# [417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

## Problem

### Description

There is an `m x n` rectangular island that borders both the Pacific Ocean and
Atlantic Ocean. The Pacific Ocean touches the island's left and top edges, and
the Atlantic Ocean touches the island's right and bottom edges.

The island is partitioned into a grid of square cells. You are given an `m x n`
integer matrix `heights` where `heights[r][c]` represents the height above sea
level of the cell at coordinate `(r, c)`.

The island receives a lot of rain, and the rain water can flow to neighboring
cells directly north, south, east, and west if the neighboring cell's height is
less than or equal to the current cell's height. Water can flow from any cell
adjacent to an ocean into the ocean.

Return a 2D list of grid coordinates `result` where `result[i] = [ri, ci]`
denotes that rain water can flow from cell `(ri, ci)` to both the Pacific and
Atlantic oceans.

### Constraints

* `m == heights.length`
* `n == heights[r].length`
* `1 <= m, n <= 200`
* `0 <= heights[r][c] <= 10^5`

### Examples

![image](/leetcode/400%20-%20499/resources/417/ex1.jpg)

```text
Input: heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

```text
Input: heights = [[2,1],[1,2]]
Output: [[0,0],[0,1],[1,0],[1,1]]
```

## Solutions

### DFS

Run a DFS from each ocean and mark the reachable cells using bitmasks. The
solution is all the cells that are marked with both ocean's bitmasks:

```rust
pub fn pacific_atlantic(heights: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    let mut grid = vec![vec![0u8; heights[0].len()]; heights.len()];

    for c in 0..heights[0].len() {
        mark(&mut grid, &heights, 0, c, 0); // use bit 0 for the pacific ocean
    }

    for c in 0..heights[0].len() {
        mark(&mut grid, &heights, heights.len() - 1, c, 1); // use bit 1 for the atlantic ocean
    }
    for r in 0..heights.len() {
        mark(&mut grid, &heights, r, 0, 0);
    }

    for r in 0..heights.len() {
        mark(&mut grid, &heights, r, heights[r].len() - 1, 1);
    }

    let mut solution = vec![];
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == (1 | 1 << 1) {
                solution.push(vec![r as i32, c as i32]);
            }
        }
    }
    solution
}

fn mark(grid: &mut Vec<Vec<u8>>, heights: &Vec<Vec<i32>>, r: usize, c: usize, bit: u32) {
    if grid[r][c] & (1 << bit) != 0 {
        return;
    }

    grid[r][c] |= 1 << bit;

    if r > 0 && heights[r - 1][c] >= heights[r][c] {
        mark(grid, heights, r - 1, c, bit);
    }

    if c > 0 && heights[r][c - 1] >= heights[r][c] {
        mark(grid, heights, r, c - 1, bit);
    }

    if c < heights[0].len() - 1 && heights[r][c + 1] >= heights[r][c] {
        mark(grid, heights, r, c + 1, bit);
    }

    if r < heights.len() - 1 && heights[r + 1][c] >= heights[r][c] {
        mark(grid, heights, r + 1, c, bit);
    }
}
```

## Related Problems

* [130. Surrounded Regions](/leetcode/100%20-%20199/130%20-%20Surrounded%20Regions.md)
* [200. Number of Islands](/leetcode/200%20-%20299/200%20-%20Number%20of%20Islands.md)
* [695. Max Area of Island](/leetcode/600%20-%20699/695%20-%20Max%20Area%20of%20Island.md)
* [1020. Number of Enclaves](/leetcode/1000%20-%201099/1020%20-%20Number%20of%20Enclaves.md)
* [1254. Number of Closed Islands](/leetcode/1200%20-%201299/1254%20-%20Number%20of%20Closed%20Islands.md)
* [1905. Count Sub Islands](/leetcode/1900%20-%201999/1905%20-%20Count%20Sub%20Islands.md)