# [51. N-Queens](https://leetcode.com/problems/n-queens/)

## Problem

### Description

The n-queens puzzle is the problem of placing `n` queens on an `n x n`
chessboard such that no two queens attack each other.

Given an integer `n`, return all distinct solutions to the n-queens puzzle. You
may return the answer in any order.

Each solution contains a distinct board configuration of the n-queens'
placement, where `Q` and `.` both indicate a queen and an empty space,
respectively.

### Constraints

* `1 <= n <= 9`

### Examples

#### Example 1

![image](resources/51/ex1.jpg)

#### Example 2

```text
Input: n = 1
Output: [["Q"]]
```

```text
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above
```

## Solutions

### Precomputing all valid positions using bitmasks

```rust
pub fn solve_n_queens(n: i32) -> Vec<Vec<String>> {
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

    let mut answer = vec![];
    backtrack(
        &mut answer,
        &mut vec![vec![b'.'; n]; n],
        &allowed_positions,
        all_board_bits,
        n,
        0,
    );
    answer
}

fn backtrack(
    answer: &mut Vec<Vec<String>>,
    variation: &mut Vec<Vec<u8>>,
    allowed_positions: &[u128],
    allowed: u128,
    size: usize,
    row: usize,
) {
    if row == size {
        let board = variation
            .iter()
            // SAFETY: the array can contain only b'.' and b'Q' which is valid UTF-8
            .map(|x| unsafe { std::str::from_utf8_unchecked(&x) })
            .map(|x| x.to_owned())
            .collect();
        answer.push(board);
        return;
    }

    if allowed == 0 {
        // we don't have any valid positions left
        return;
    }

    for col in 0..size {
        // skip forbidden position
        if allowed & ((1 << col) << (row * size)) == 0 {
            continue;
        }

        variation[row][col] = b'Q';
        let allowed = allowed & allowed_positions[row * size + col];
        backtrack(answer, variation, allowed_positions, allowed, size, row + 1);
        variation[row][col] = b'.';
    }
}
```
