# [37. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)

## Problem

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy all of the following rules:

* Each of the digits 1-9 must occur exactly once in each row.
* Each of the digits 1-9 must occur exactly once in each column.
* Each of the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes
  of the grid.

The `.` character indicates empty cells.

## Solution

### Brute-Force solution

```rust

pub fn solve(board: &mut Vec<Vec<char>>) -> bool {
    assert_eq!(board.len(), 9);

    for r in 0..board.len() {
        assert_eq!(board[r].len(), 9);

        for c in 0..board[r].len() {
            if board[r][c] == '.' {
                for ch in '1'..='9' {
                    if is_valid_char_for_position(board, r, c, ch) {
                        board[r][c] = ch;

                        if solve(board) {
                            return true;
                        }

                        board[r][c] = '.';
                    }
                }

                return false;
            }
        }
    }

    true
}

fn is_valid_char_for_position(board: &Vec<Vec<char>>, r: usize, c: usize, ch: char) -> bool {
    if board[r].contains(&ch) {
        return false;
    }

    for row in 0..9 {
        if board[row][c] == ch {
            return false;
        }
    }

    // check a 3x3 block
    for row in (r / 3) * 3..((r / 3) * 3 + 3) {
        for col in (c / 3) * 3..((c / 3) * 3 + 3) {
            if board[row][col] == ch {
                return false;
            }
        }
    }

    true
}
```

A minor optimisation is to continue the recursion from the last processed row
instead of starting from the beginning:

```rust
pub fn solve_sudoku(board: &mut Vec<Vec<char>>) {
    solve(board, 0);
}

pub fn solve(board: &mut Vec<Vec<char>>, start_row: usize) -> bool {
    assert_eq!(board.len(), 9);

    for r in start_row..board.len() {
        assert_eq!(board[r].len(), 9);

        for c in 0..board[r].len() {
            if board[r][c] == '.' {
                for ch in '1'..='9' {
                    if is_valid_char_for_position(board, r, c, ch) {
                        board[r][c] = ch;

                        if solve(board, r) {
                            return true;
                        }

                        board[r][c] = '.';
                    }
                }

                return false;
            }
        }
    }

    true
}

fn is_valid_char_for_position(board: &Vec<Vec<char>>, r: usize, c: usize, ch: char) -> bool {
    if board[r].contains(&ch) {
        return false;
    }

    for row in 0..9 {
        if board[row][c] == ch {
            return false;
        }
    }

    for row in (r / 3) * 3..((r / 3) * 3 + 3) {
        for col in (c / 3) * 3..((c / 3) * 3 + 3) {
            if board[row][col] == ch {
                return false;
            }
        }
    }

    true
}
```

## Related Problems

* [36. Valid Sudoku](36%20-%20Valid%20Sudoku.md)