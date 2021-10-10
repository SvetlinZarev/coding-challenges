# [36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)

## Problem

Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be
validated according to the following rules:

* Each row must contain the digits 1-9 without repetition.
* Each column must contain the digits 1-9 without repetition.
* Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9
  without repetition.

**Note:**

* A Sudoku board (partially filled) could be valid but is not necessarily
  solvable.
* Only the filled cells need to be validated according to the mentioned rules.

#### Examples

```text
Input: board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: true
```

```text
Input: board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being 
modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
```

## Solution

```rust
pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
    let mut set = HashSet::new();

    for row in board.iter() {
        set.clear();
        for v in row.iter().copied().filter(|&ch| ch != '.') {
            if !set.insert(v) {
                return false;
            }
        }
    }

    for col in 0..board[0].len() {
        set.clear();
        for row in 0..board.len() {
            let ch = board[row][col];
            if ch != '.' {
                if !set.insert(ch) {
                    return false;
                }
            }
        }
    }

    drop(set);

    let mut a = HashSet::new();
    let mut b = HashSet::new();
    let mut c = HashSet::new();
    for row in 0..board.len() {
        if row % 3 == 0 {
            a.clear();
            b.clear();
            c.clear();
        }

        for col in 0..board[0].len() {
            let set = match col {
                0 | 1 | 2 => &mut a,
                3 | 4 | 5 => &mut b,
                6 | 7 | 8 => &mut c,
                _ => unreachable!(),
            };

            let ch = board[row][col];
            if ch != '.' {
                if !set.insert(ch) {
                    return false;
                }
            }
        }
    }

    true
}
```