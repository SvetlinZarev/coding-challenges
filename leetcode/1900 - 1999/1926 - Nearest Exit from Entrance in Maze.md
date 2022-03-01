# [1926. Nearest Exit from Entrance in Maze](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze/)

## Problem

### Description

You are given an `m x n` matrix maze (0-indexed) with empty cells (represented
as '.') and walls (represented as '+'). You are also given the entrance of the
maze, where `entrance = [entrancerow, entrancecol]` denotes the row and column
of the cell you are initially standing at.

In one step, you can move one cell up, down, left, or right. You cannot step
into a cell with a wall, and you cannot step outside the maze. Your goal is to
find the nearest exit from the entrance. An exit is defined as an empty cell
that is at the border of the maze. The entrance does not count as an exit.

Return the number of steps in the shortest path from the entrance to the nearest
exit, or `-1` if no such path exists.

### Constraints

* `maze.length == m`
* `maze[i].length == n`
* `1 <= m, n <= 100`
* `maze[i][j]` is either '.' or '+'.
* `entrance.length == 2`
* `0 <= entrancerow < m`
* `0 <= entrancecol < n`
* `entrance` will always be an empty cell.

### Examples

![image](/leetcode/1900%20-%201999/resources/1926/ex1.jpg)

```text
Input: maze = [["+","+",".","+"],[".",".",".","+"],["+","+","+","."]], entrance = [1,2]
Output: 1
Explanation: There are 3 exits in this maze at [1,0], [0,2], and [2,3].
Initially, you are at the entrance cell [1,2].
- You can reach [1,0] by moving 2 steps left.
- You can reach [0,2] by moving 1 step up.
It is impossible to reach [2,3] from the entrance.
Thus, the nearest exit is [0,2], which is 1 step away.
```

![image](/leetcode/1900%20-%201999/resources/1926/ex2.jpg)

```text
Input: maze = [["+","+","+"],[".",".","."],["+","+","+"]], entrance = [1,0]
Output: 2
Explanation: There is 1 exit in this maze at [1,2].
[1,0] does not count as an exit since it is the entrance cell.
Initially, you are at the entrance cell [1,0].
- You can reach [1,2] by moving 2 steps right.
Thus, the nearest exit is [1,2], which is 2 steps away.
```

![image](/leetcode/1900%20-%201999/resources/1926/ex3.jpg)

```text
Input: maze = [[".","+"]], entrance = [0,0]
Output: -1
Explanation: There are no exits in this maze.
```

## Solutions

### BFS

```rust
use std::collections::VecDeque;

const PATH: char = '.';
const VISITED: char = '@';
const NO_EXIT_FOUND: i32 = -1;

pub fn nearest_exit(mut maze: Vec<Vec<char>>, entrance: Vec<i32>) -> i32 {
    let r_start = entrance[0] as usize;
    let c_start = entrance[1] as usize;

    let c0 = 0;
    let c1 = maze[0].len() - 1;

    let r0 = 0;
    let r1 = maze.len() - 1;

    let mut queue = VecDeque::new();
    queue.push_back((r_start, c_start, 0));

    while let Some((r, c, len)) = queue.pop_front() {
        if r == r0 || r == r1 || c == c0 || c == c1 {
            // only one of the current r/c needs to be different from the starting r/c
            if r != r_start || c != c_start {
                return len;
            }
        }

        if maze[r][c] == VISITED {
            continue;
        }
        maze[r][c] = VISITED;

        if r > 0 && maze[r - 1][c] == PATH {
            queue.push_back((r - 1, c, len + 1));
        }

        if c > 0 && maze[r][c - 1] == PATH {
            queue.push_back((r, c - 1, len + 1));
        }

        if c < maze[r].len() - 1 && maze[r][c + 1] == PATH {
            queue.push_back((r, c + 1, len + 1));
        }

        if r < maze.len() - 1 && maze[r + 1][c] == PATH {
            queue.push_back((r + 1, c, len + 1));
        }
    }

    NO_EXIT_FOUND
}
```