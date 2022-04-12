# [289. Game of Life](https://leetcode.com/problems/game-of-life/)

## Problems

### Description

According
to [Wikipedia's article](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life): "
The Game of Life, also known simply as Life, is a cellular automaton devised by
the British mathematician John Horton Conway in 1970."

The board is made up of an `m x n` grid of cells, where each cell has an initial
state: live (represented by a `1`) or dead (represented by a `0`). Each cell
interacts with its eight neighbors (horizontal, vertical, diagonal) using the
following four rules (taken from the above Wikipedia article):

* Any live cell with fewer than two live neighbors dies as if caused by
  under-population.
* Any live cell with two or three live neighbors lives on to the next
  generation.
* Any live cell with more than three live neighbors dies, as if by
  over-population.
* Any dead cell with exactly three live neighbors becomes a live cell, as if by
  reproduction.

The next state is created by applying the above rules simultaneously to every
cell in the current state, where births and deaths occur simultaneously. Given
the current state of the `m x n` grid `board`, return the next state.

#### Follow-up

* Could you solve it in-place? Remember that the board needs to be updated
  simultaneously: You cannot update some cells first and then use their updated
  values to update other cells.

* In this question, we represent the board using a 2D array. In principle, the
  board is infinite, which would cause problems when the active area encroaches
  upon the border of the array (i.e., live cells reach the border). How would
  you address these problems?

### Constraints

* `m == board.length`
* `n == board[i].length`
* `1 <= m, n <= 25`
* `board[i][j] is 0 or 1.`

### Examples

```text
Input: board = [[0,1,0],[0,0,1],[1,1,1],[0,0,0]]
Output: [[0,0,0],[1,0,1],[0,1,1],[0,1,0]]
```

```text
Input: board = [[1,1],[1,0]]
Output: [[1,1],[1,1]]
```

## Solutions

### In-place simulation

```rust
// Because we need to modify it in-place and the board contains only 0 or 1
// we can use the rest of the bits to store the next state. The 0th bit is
// the current state and the 1st bit is the next state.
// After processing all cells we can just shift-right to obtain the next state
pub fn game_of_life(board: &mut Vec<Vec<i32>>) {
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            let mut count = 0;

            // upper row
            if r > 0 {
                if c > 0 && board[r - 1][c - 1] & 1 == 1 {
                    count += 1;
                }
                if board[r - 1][c] & 1 == 1 {
                    count += 1;
                }
                if c < board[r].len() - 1 && board[r - 1][c + 1] & 1 == 1 {
                    count += 1;
                }
            }

            // current row
            {
                if c > 0 && board[r][c - 1] & 1 == 1 {
                    count += 1;
                }
                if c < board[r].len() - 1 && board[r][c + 1] & 1 == 1 {
                    count += 1;
                }
            }

            // bottom row
            if r < board.len() - 1 {
                if c > 0 && board[r + 1][c - 1] & 1 == 1 {
                    count += 1;
                }
                if board[r + 1][c] & 1 == 1 {
                    count += 1;
                }
                if c < board[r].len() - 1 && board[r + 1][c + 1] & 1 == 1 {
                    count += 1;
                }
            }

            board[r][c] |= (((count == 3) || (board[r][c] & 1 == 1 && count == 2)) as i32) << 1;
        }
    }

    for r in 0..board.len() {
        for c in 0..board[r].len() {
            board[r][c] = board[r][c] >> 1;
        }
    }
}
```