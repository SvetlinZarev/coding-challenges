# [1905. Count Sub Islands](https://leetcode.com/problems/count-sub-islands/)

## Problem

### Description

You are given two `m x n` binary matrices `grid1` and `grid2` containing
only `0`'s (representing water) and `1`'s (representing land). An island is a
group of `1`'s connected 4-directionally (horizontal or vertical). Any cells
outside of the grid are considered water cells.

An island in `grid2` is considered a sub-island if there is an island in `grid1`
that contains all the cells that make up this island in `grid2`.

Return the number of islands in `grid2` that are considered sub-islands.

### Constraints

* `m == grid1.length == grid2.length`
* `n == grid1[i].length == grid2[i].length`
* `1 <= m, n <= 500`
* `grid1[i][j]` and `grid2[i][j]` are either `0` or `1`.

### Examples

![image](/leetcode/1900%20-%201999/resources/1905/ex1.png)

```text
Input: grid1 = [[1,1,1,0,0],[0,1,1,1,1],[0,0,0,0,0],[1,0,0,0,0],[1,1,0,1,1]], grid2 = [[1,1,1,0,0],[0,0,1,1,1],[0,1,0,0,0],[1,0,1,1,0],[0,1,0,1,0]]
Output: 3
Explanation: In the picture above, the grid on the left is grid1 and the grid on the right is grid2.
The 1s colored red in grid2 are those considered to be part of a sub-island. There are three sub-islands.
```

![image](/leetcode/1900%20-%201999/resources/1905/ex2.png)

```text
Input: grid1 = [[1,0,1,0,1],[1,1,1,1,1],[0,0,0,0,0],[1,1,1,1,1],[1,0,1,0,1]], grid2 = [[0,0,0,0,0],[1,1,1,1,1],[0,1,0,1,0],[0,1,0,1,0],[1,0,0,0,1]]
Output: 2 
Explanation: In the picture above, the grid on the left is grid1 and the grid on the right is grid2.
The 1s colored red in grid2 are those considered to be part of a sub-island. There are two sub-islands.
```

## Solution

### Mark and count

* Iterate over each cell in the matrix
* Mark the connected *land* cells, in order to process them only once
* Cross-check with the first matrix if they are *land* cells there as well

```rust
const WATER: i32 = 0;
const LAND: i32 = 1;

pub fn count_sub_islands(grid1: Vec<Vec<i32>>, mut grid2: Vec<Vec<i32>>) -> i32 {
    assert_eq!(grid1.len(), grid2.len());

    let mut sub_islands = 0;
    for r in 0..grid2.len() {
        assert_eq!(grid1[r].len(), grid2[r].len());

        for c in 0..grid2[r].len() {
            if grid2[r][c] == LAND {
                if mark_island(&grid1, &mut grid2, r, c) {
                    sub_islands += 1;
                }
            }
        }
    }

    sub_islands
}

fn mark_island(grid1: &Vec<Vec<i32>>, grid2: &mut Vec<Vec<i32>>, r: usize, c: usize) -> bool {
    let mut stack = vec![];
    stack.push((r, c));

    let mut is_sub_island = true;
    while let Some((r, c)) = stack.pop() {
        if grid2[r][c] != LAND {
            continue;
        }
        grid2[r][c] = WATER;

        if is_sub_island && grid1[r][c] != LAND {
            is_sub_island = false;
        }

        if r > 0 && grid2[r - 1][c] == LAND {
            stack.push((r - 1, c));
        }
        if c > 0 && grid2[r][c - 1] == LAND {
            stack.push((r, c - 1));
        }
        if c < grid2[r].len() - 1 && grid2[r][c + 1] == LAND {
            stack.push((r, c + 1));
        }
        if r < grid2.len() - 1 && grid2[r + 1][c] == LAND {
            stack.push((r + 1, c));
        }
    }

    is_sub_island
}
```

## Related Problems
* [130. Surrounded Regions](/leetcode/100%20-%20199/130%20-%20Surrounded%20Regions.md)
* [200. Number of Islands](/leetcode/200%20-%20299/200%20-%20Number%20of%20Islands.md)
* [695. Max Area of Island](/leetcode/600%20-%20699/695%20-%20Max%20Area%20of%20Island.md)
* [1020. Number of Enclaves](/leetcode/1000%20-%201099/1020%20-%20Number%20of%20Enclaves.md)
* [1254. Number of Closed Islands](/leetcode/1200%20-%201299/1254%20-%20Number%20of%20Closed%20Islands.md)