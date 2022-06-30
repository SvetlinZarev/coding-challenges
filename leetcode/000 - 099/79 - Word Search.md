# [79. Word Search](https://leetcode.com/problems/word-search/)

## Problem

### Description

Given an `m x n` grid of characters board and a string word, return true if word
exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where
adjacent cells are horizontally or vertically neighboring. The same letter cell
may not be used more than once.

### Examples

**Input:**

```text
board = [
  ["A","B","C","E"],
  ["S","F","C","S"],
  ["A","D","E","E"]
], 

word = "ABCCED"
```

**Output:** `true`

### Constraints

* `m == board.length`
* `n = board[i].length`
* `1 <= m, n <= 6`
* `1 <= word.length <= 15`
* `board` and `word` consists of only lowercase and uppercase English letters.

## Solutions

### Using BFS/DFS

* Time: 52 ms
* Memory: 2MB

```rust
impl Solution {
    pub fn exist(board: Vec<Vec<char>>, word: String) -> bool {
        exist(board, word)
    }
}

const RMAX: usize = 6;

// We can use a macro to avoid duplicating the same code over and over again
macro_rules! handle_cell {
    ($queue:expr, $row: expr, $col: expr, $idx: expr, $mask: expr,  $board: expr, $word: expr) => {
        if is_available($mask, $row, $col) {
            if $board[$row][$col] == $word[$idx] {
                // we've just found the last letter of the word
                if $idx + 1 == $word.len() {
                    return true;
                }
                
                // if it's not the last letter, add the neighbouring cells in the queue
                $queue.push(($row, $col, $idx + 1, mark_used($mask, $row, $col)));
            }
        }
    };
}

pub fn exist(board: Vec<Vec<char>>, word: String) -> bool {
    assert!(word.len() <= 15);
    assert!(board.len() >= 1 && board.len() <= 6);
    assert!(board[0].len() >= 1 && board[0].len() <= 6);

    let word = word.chars().collect::<Vec<_>>();
    let mut queue = vec![];

    // find all coordinates which contain the first letter of the target word
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            if board[r][c] == word[0] {
                // If the target word is only 1 character long, then we have just found it!
                if word.len() == 1 {
                    return true;
                }

                queue.push((r, c, 1usize, mark_used(0, r, c)));
            }
        }
    }

    // if the board does not contain the starting 
    // letter, then it does not contain the word
    if queue.is_empty() {
        return false;
    }

    while let Some((r, c, next_word_idx, bitmask)) = queue.pop() {
        //top
        if r > 0 {
            handle_cell!(queue, r - 1, c, next_word_idx, bitmask, board, word);
        }

        //left
        if c > 0 {
            handle_cell!(queue, r, c - 1, next_word_idx, bitmask, board, word);
        }

        //right
        if c < board[r].len() - 1 {
            handle_cell!(queue, r, c + 1, next_word_idx, bitmask, board, word);
        }

        //bottom
        if r < board.len() - 1 {
            handle_cell!(queue, r + 1, c, next_word_idx, bitmask, board, word);
        }
    }

    false
}

fn mark_used(mask: u64, r: usize, c: usize) -> u64 {
    mask | 1 << ((r * RMAX) + c)
}

fn is_available(mask: u64, r: usize, c: usize) -> bool {
    mask & 1 << ((r * RMAX) + c) == 0
}
```

Due to the iterative implementation we cannot modify the input grid in order to
mark the visited cells. Instead, we take advantage of the problem constraints
that the rid side cannot be more than `6` elements wide. Thus, it's not possible
to have more than `6 * 6 = 36` cells, so we can use a bit-field to remember
which elements have already been used

### Using a Trie

We can use the same solution as
in [Word Search II](/200%20-%20299/212%20-%20Word%20Search%20II.md)

## Related Problems

* [212. Word Search II](/leetcode/200%20-%20299/212%20-%20Word%20Search%20II.md)
