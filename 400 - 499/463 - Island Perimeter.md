# [463. Island Perimeter](https://leetcode.com/problems/island-perimeter/)

## Problem

You are given `row x col` grid representing a map where `grid[i][j] = 1`
represents land and `grid[i][j] = 0` represents water.

Grid cells are connected horizontally/vertically (not diagonally). The grid is
completely surrounded by water, and there is exactly one island (i.e., one or
more connected land cells).

The island doesn't have "lakes", meaning the water inside isn't connected to the
water around the island. One cell is a square with side length 1. The grid is
rectangular, width and height don't exceed 100. Determine the perimeter of the
island.

#### Constraints

* `row == grid.length`
* `col == grid[i].length`
* `1 <= row, col <= 100`
* `grid[i][j] is 0 or 1`
* There is exactly one island in grid.

#### Example

```text
Input: grid = [[0,1,0,0],[1,1,1,0],[0,1,0,0],[1,1,0,0]]
Output: 16
```

## Solution

### Flood-fill like approach

```rust
pub fn island_perimeter(mut grid: Vec<Vec<i32>>) -> i32 {
    let mut perimeter = 0;
    let mut queue = Vec::with_capacity(16);

    'seed: for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == 1 {
                queue.push((r, c));
                break 'seed;
            }
        }
    }

    if queue.is_empty() {
        return perimeter;
    }

    while let Some((r, c)) = queue.pop() {
        if grid[r][c] != 1 {
            continue;
        }

        // clear the current cell
        grid[r][c] = 2;

        //left
        if c == 0 {
            perimeter += 1;
        } else {
            match grid[r][c - 1] {
                0 => perimeter += 1,
                1 => queue.push((r, c - 1)),
                _ => {}
            }
        }

        //right
        if c == grid[r].len() - 1 {
            perimeter += 1;
        } else {
            match grid[r][c + 1] {
                0 => perimeter += 1,
                1 => queue.push((r, c + 1)),
                _ => {}
            }
        }

        //top
        if r == 0 {
            perimeter += 1;
        } else {
            match grid[r - 1][c] {
                0 => perimeter += 1,
                1 => queue.push((r - 1, c)),
                _ => {}
            }
        }

        //bottom
        if r == grid.len() - 1 {
            perimeter += 1;
        } else {
            match grid[r + 1][c] {
                0 => perimeter += 1,
                1 => queue.push((r + 1, c)),
                _ => {}
            }
        }
    }

    perimeter
}
```

### Iterative approach

We can take advantage of the constraint that there is only 1 island. Then we can
just iterate over all cells, incrementing a counter for each "water"
neighbour when the current cell is "land"