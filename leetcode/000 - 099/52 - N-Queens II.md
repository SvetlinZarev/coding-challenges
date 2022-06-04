# [52. N-Queens II](https://leetcode.com/problems/n-queens-ii/)

## Problem

### Description

The n-queens puzzle is the problem of placing `n` queens on an `n x n`
chessboard such that no two queens attack each other.

Given an integer `n`, return the number of distinct solutions to the n-queens
puzzle.

### Constraints

* `1 <= n <= 9`

### Examples

#### Example 1

![image](resources/52/ex1.jpg)

```text
Input: n = 4
Output: 2
Explanation: There are two distinct solutions to the 4-queens puzzle as shown.
```

#### Example 2

```text
Input: n = 1
Output: 1
```

## Solutions

### Backtracking

```rust
pub fn total_n_queens(n: i32) -> i32 {
    assert!(n >= 1 && n <= 9);
    let n: usize = n.try_into().unwrap();

    backtrack(&mut vec![vec![b'.'; n]; n], 0)
}

fn backtrack(board: &mut Vec<Vec<u8>>, row: usize) -> i32 {
    if row == board.len() {
        return 1;
    }

    let mut answer = 0;

    for col in 0..board.len() {
        if is_allowed(board, row, col) {
            board[row][col] = b'Q';
            answer += backtrack(board, row + 1);
            board[row][col] = b'.';
        }
    }

    answer
}

// check if placing a queen at <row, col> violates the queen positioning rules
fn is_allowed(board: &[Vec<u8>], row: usize, col: usize) -> bool {
    let n = board.len();

    for r in 0..n {
        if board[r][col] == b'Q' {
            return false;
        }
    }

    // Check top-left diagonal
    let mut r = row;
    let mut c = col;
    while r > 0 && c > 0 {
        r -= 1;
        c -= 1;

        if board[r][c] == b'Q' {
            return false;
        }
    }

    // Check top-right diagonal
    r = row;
    c = col;
    while r > 0 && c < n - 1 {
        r -= 1;
        c += 1;

        if board[r][c] == b'Q' {
            return false;
        }
    }


    // There is no need to check the bottom left & right diagonals, because 
    // we are moving from top to bottom - i.e. there ar eno queens placed below 
    // the current row, so this method cannot return false in that case

    true
}
```

### Backtracking + precomputing all valid positions after single queen placement

```rust
pub fn total_n_queens(n: i32) -> i32 {
    assert!(n >= 1 && n <= 9);
    let n: usize = n.try_into().unwrap();

    // set all N*N positions of the board to 1
    let all_board_bits = (1 << (n * n)) - 1;

    let mut allowed_positions = vec![0u128; n * n];

    // Precompute all remaining valid positions after we place a single
    // queen on the board.
    //
    // Then we can just use bitwise AND to quickly filter out all invalid
    // positions when we start placing more queens
    for row in 0..n {
        for col in 0..n {
            // set all N*N positions of the board as "allowed"
            let mut board = all_board_bits;

            // calculate an "all 1" mask for all bits of the current row
            let row_mask = ((1 << n) - 1) << (row * n);

            // invert the mask, making the current row bts 0, and AND it
            // with the board, thus setting all current row bits to 0
            board &= !row_mask;

            // set all bits in the current column to 0
            for r in 0..n {
                // set the current column bit at row `r` to 0
                board &= !((1 << col) << (r * n));
            }

            // Clear top-left diagonal
            let mut r = row;
            let mut c = col;
            while r > 0 && c > 0 {
                r -= 1;
                c -= 1;

                board &= !((1 << c) << (r * n));
            }

            // Clear top-right diagonal
            r = row;
            c = col;
            while r > 0 && c < n - 1 {
                r -= 1;
                c += 1;

                board &= !((1 << c) << (r * n));
            }

            // Clear bottom-left diagonal
            r = row;
            c = col;
            while r < n - 1 && c > 0 {
                r += 1;
                c -= 1;

                board &= !((1 << c) << (r * n));
            }

            // Clear bottom-right diagonal
            r = row;
            c = col;
            while r < n - 1 && c < n - 1 {
                r += 1;
                c += 1;

                board &= !((1 << c) << (r * n));
            }

            allowed_positions[row * n + col] = board;
        }
    }

    backtrack(
        &mut vec![vec![b'.'; n]; n],
        &allowed_positions,
        all_board_bits,
        0,
    )
}

fn backtrack(
    board: &mut Vec<Vec<u8>>,
    allowed_positions: &[u128],
    allowed: u128,
    row: usize,
) -> i32 {
    if row == board.len() {
        return 1;
    }

    if allowed == 0 {
        // we don't have any valid positions left
        return 0;
    }

    let mut answer = 0;

    for col in 0..board.len() {
        // skip forbidden position
        if allowed & ((1 << col) << (row * board.len())) == 0 {
            continue;
        }

        board[row][col] = b'Q';
        let allowed = allowed & allowed_positions[row * board.len() + col];
        answer += backtrack(board, allowed_positions, allowed, row + 1);
        board[row][col] = b'.';
    }

    answer
}
```

Running a criterion benchmark on both solutions reveals that the one
precomputing the allowed positions is around 3x faster:

```text
V1:    time:   [1.0089 ms 1.0101 ms 1.0115 ms]               
V2:    time:   [367.87 us 369.13 us 370.50 us]              
```

## Related Problems

* [51. N-Queens](51%20-%20N-Queens.md)
