# [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

## Problem

Given an `m x n` 2D binary grid `grid` which represents a map of `1`s (land)
and `0`s (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands
horizontally or vertically. You may assume all four edges of the grid are all
surrounded by water.

#### Examples

```text
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

```text
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

#### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 300`
* `grid[i][j] is '0' or '1'`

## Solutions

### Using a bfs/flood-fill approach

If we are on a land:

* `push_back()` the neighbouring land, as it's part of the current island
* `push_front()` the neighbouring water, in order to act as a separator

If we re on water:

* `push_front()` the neighbouring water, in order to act as a separator
* `push_front()` the current tile then `push_front()` the neighbouring land

We need to separate the land tiles with water tiles in the queue, because we use
the transition from water to land to count the number of islands. So when we
push back the neighbouring land, the land tiles are not separated, so we do not
increase the islands counter

We mark the visited tiles in place, but mark the land/water tiles differently
because we still need them to decide whether to increase the counter

* Runtime: 12 ms
* Memory Usage: 9.3 MB

```rust
use std::collections::VecDeque;

pub fn num_islands(mut grid: Vec<Vec<char>>) -> i32 {
    let mut islands = 0;
    let mut queue = VecDeque::with_capacity(32);
    queue.push_back((0, 0));

    let mut was_water = true;
    while let Some((r, c)) = queue.pop_back() {
        match grid[r][c] {
            '0' => was_water = true,
            '1' => {
                if was_water {
                    islands += 1;
                    was_water = false;
                }
            }
            'X' => {
                was_water = false;
                continue;
            }
            _ => {
                was_water = true;
                continue;
            }
        }

        //left
        if c > 0 {
            match grid[r][c - 1] {
                '0' => queue.push_front((r, c - 1)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r, c - 1));
                    } else {
                        queue.push_back((r, c - 1));
                    }
                }
                _ => {}
            }
        }

        // center
        grid[r][c] = if was_water { 'O' } else { 'X' };

        //right
        if c < grid[r].len() - 1 {
            match grid[r][c + 1] {
                '0' => queue.push_front((r, c + 1)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r, c + 1));
                    } else {
                        queue.push_back((r, c + 1));
                    }
                }
                _ => {}
            }
        }

        //top
        if r > 0 {
            match grid[r - 1][c] {
                '0' => queue.push_front((r - 1, c)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r - 1, c));
                    } else {
                        queue.push_back((r - 1, c));
                    }
                }
                _ => {}
            }
        }

        //bottom
        if r < grid.len() - 1 {
            match grid[r + 1][c] {
                '0' => queue.push_front((r + 1, c)),
                '1' => {
                    if was_water {
                        queue.push_front((r, c));
                        queue.push_front((r + 1, c));
                    } else {
                        queue.push_back((r + 1, c));
                    }
                }
                _ => {}
            }
        }
    }

    islands
}
```
