# [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)

## Problem

You are given an `m x n` grid where each cell can have one of three values:
* `0` representing an empty cell
* `1` representing a fresh orange
* `2` representing a rotten orange

Every minute, any fresh orange that is 4-directionally adjacent to a rotten 
orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh 
orange. If this is impossible, return `-1`.


#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 10`
* `grid[i][j]` is `0`, `1`, or `2`


#### Examples

```text
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4
```

```text
Input: grid = [[0,2]]
Output: 0
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.
```

```text
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.
```

## Solution

```rust
pub fn oranges_rotting(mut grid: Vec<Vec<i32>>) -> i32 {
    let mut minutes = 0;
    let mut fruits = 0;

    // find all initially rotten fruits and the 
    // total number of fruits in the grid
    let mut roots = Vec::with_capacity(16);
    for r in 0..grid.len() {
        for c in 0..grid[r].len() {
            if grid[r][c] == 2 {
                roots.push((r, c));
            }

            if grid[r][c] != 0 {
                fruits += 1;
            }
        }
    }

    let mut next = Vec::with_capacity(roots.len() * 2);
    loop {
        // each loop iteration marks all fruits that will 
        // rot in 1 minutes (i.e. all neighbouring fruits)
        while let Some((r, c)) = roots.pop() {
            fruits -= 1; // track how many fruits have rotten

            //left
            if c > 0 {
                let elem = &mut grid[r][c - 1];
                if *elem == 1 {
                    *elem = 2;
                    next.push((r, c - 1));
                }
            }

            //right
            if c < grid[r].len() - 1 {
                let elem = &mut grid[r][c + 1];
                if *elem == 1 {
                    *elem = 2;
                    next.push((r, c + 1));
                }
            }

            //top
            if r > 0 {
                let elem = &mut grid[r - 1][c];
                if *elem == 1 {
                    *elem = 2;
                    next.push((r - 1, c));
                }
            }

            //bottom
            if r < grid.len() - 1 {
                let elem = &mut grid[r + 1][c];
                if *elem == 1 {
                    *elem = 2;
                    next.push((r + 1, c));
                }
            }
        }

        // if there are no new rotten fruits, then we are finished
        if next.is_empty() {
            break;
        }

        std::mem::swap(&mut roots, &mut next);
        minutes += 1;
    }

    if all fruits have rotten, return the time, else return `-1`
    if fruits == 0 {
        minutes
    } else {
        -1
    }
}
```
