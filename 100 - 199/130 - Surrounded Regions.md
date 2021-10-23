# [130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)

## Problem

Given an `m x n` matrix board containing `X` and `O`, capture all regions that
are 4-directionally surrounded by  `X`.

A region is captured by flipping all `O`s into `X`s in that surrounded region.

#### Constraints

* `m == board.length`
* `n == board[i].length`
* `1 <= m, n <= 200`
* `board[i][j]` is `X` or `O`

#### Examples

```text
Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
Explanation: Surrounded regions should not be on the border, which means that any 
'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on 
the border and it is not connected to an 'O' on the border will be flipped to 
'X'. Two cells are connected if they are adjacent cells connected horizontally
or vertically.
```

## Solution

The only "islands" of `O` that will not be converted to `X`s will be those that
touch the border the of grid. Thus, we can iterate over the first/last rows and
columns and mark the neighbouring `O`s. Then we simply switch everything that's
not marked to `X` and revert the marked cells to `O`

```rust
const MARKED: char = '!';
const FREE: char = 'O';
const CAPTURED: char = 'X';

pub fn solve(board: &mut Vec<Vec<char>>) {
    // mark cells connected to the border
    for col in 0..board[0].len() {
        if board[0][col] == FREE {
            mark_connected_cells(board, 0, col);
        }
    }
    for col in 0..board[board.len() - 1].len() {
        if board[board.len() - 1][col] == FREE {
            mark_connected_cells(board, board.len() - 1, col);
        }
    }
    for row in 1..board.len() - 1 {
        if board[row][0] == FREE {
            mark_connected_cells(board, row, 0);
        }

        if board[row][board[row].len() - 1] == FREE {
            mark_connected_cells(board, row, board[row].len() - 1);
        }
    }

    //mark the remaining FREE cells as CAPTURED and the MARKED cells as FREE
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            if board[r][c] == MARKED {
                board[r][c] = FREE;
            } else if board[r][c] == FREE {
                board[r][c] = CAPTURED;
            }
        }
    }
}

fn mark_connected_cells(board: &mut Vec<Vec<char>>, r: usize, c: usize) {
    if board[r][c] != FREE {
        return;
    }

    board[r][c] = MARKED;

    if r > 0 {
        mark_connected_cells(board, r - 1, c);
    }
    if c > 0 {
        mark_connected_cells(board, r, c - 1);
    }
    if c < board[r].len() - 1 {
        mark_connected_cells(board, r, c + 1);
    }
    if r < board.len() - 1 {
        mark_connected_cells(board, r + 1, c);
    }
}
```

## Related problems

* [200. Number of Islands](/200%20-%20299/200%20-%20Number%20of%20Islands.md)
* [1020. Number of Enclaves](/1000%20-%201099/1020%20-%20Number%20of%20Enclaves.md)
* [1254. Number of Closed Islands](/1200%20-%201299/1254%20-%20Number%20of%20Closed%20Islands.md)