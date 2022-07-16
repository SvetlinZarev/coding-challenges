# [576. Out of Boundary Paths](https://leetcode.com/problems/out-of-boundary-paths/)

## Problem

### Description

There is an `m x n` grid with a ball. The ball is initially at the
position `[startRow, startColumn]`. You are allowed to move the ball to one of
the four adjacent cells in the grid (possibly out of the grid crossing the grid
boundary). You can apply at most `maxMove` moves to the ball.

Given the five integers `m`, `n`, `maxMove`, `startRow`, `startColumn`, return
the number of paths to move the ball out of the grid boundary. Since the answer
can be very large, return it `modulo 10^9 + 7`.

### Constraints

* `1 <= m, n <= 50`
* `0 <= maxMove <= 50`
* `0 <= startRow < m`
* `0 <= startColumn < n`

### Examples

#### Example 1

![image](resources/576/ex1.png)

```text
Input: m = 2, n = 2, maxMove = 2, startRow = 0, startColumn = 0
Output: 6
```

#### Example 2

![image](resources/576/ex2.png)

```text
Input: m = 1, n = 3, maxMove = 3, startRow = 0, startColumn = 1
Output: 12
```

## Solutions

### Bruteforce (time limit exceeded)

```rust
const MOD: i32 = 10i32.pow(9) + 7;

pub fn find_paths(m: i32, n: i32, max_move: i32, start_row: i32, start_column: i32) -> i32 {
    // We cannot perform the move, thus we have to return 0
    if max_move < 0 {
        return 0;
    }

    // The ball is out of the grid's boundary -> i.e we've found 1 way to do it
    if start_row < 0 || start_row >= m || start_column < 0 || start_column >= n {
        return 1;
    }

    let mut paths = 0;

    // up
    paths += find_paths(m, n, max_move - 1, start_row - 1, start_column);
    paths %= MOD;

    // left
    paths += find_paths(m, n, max_move - 1, start_row, start_column - 1);
    paths %= MOD;

    // right
    paths += find_paths(m, n, max_move - 1, start_row, start_column + 1);
    paths %= MOD;

    // down
    paths += find_paths(m, n, max_move - 1, start_row + 1, start_column);
    paths %= MOD;

    paths
}
```

### DFS with memoization (top-down DP)

```rust
const MOD: i32 = 10i32.pow(9) + 7;

pub fn find_paths(m: i32, n: i32, max_moves: i32, start_row: i32, start_column: i32) -> i32 {
    assert!(m > 0 && m <= 50);
    assert!(n > 0 && n <= 50);
    assert!(max_moves >= 0);

    if max_moves == 0 {
        return 0;
    }

    // use `-1` as sentinel value, because 0 is a valid return value
    let mut cache = vec![vec![vec![-1; n as usize]; m as usize]; 1 + max_moves as usize];
    dfs(&mut cache, max_moves, start_row, start_column)
}

pub fn dfs(cache: &mut Vec<Vec<Vec<i32>>>, moves: i32, row: i32, col: i32) -> i32 {
    if moves < 0 {
        return 0;
    }

    if row < 0 || col < 0 {
        return 1;
    }

    if row as usize >= cache[moves as usize].len() || col as usize >= cache[moves as usize][row as usize].len() {
        return 1;
    }

    if cache[moves as usize][row as usize][col as usize] >= 0 {
        return cache[moves as usize][row as usize][col as usize];
    }

    let mut paths = 0;

    // up
    paths += dfs(cache, moves - 1, row - 1, col);
    paths %= MOD;

    // left
    paths += dfs(cache, moves - 1, row, col - 1);
    paths %= MOD;

    // right
    paths += dfs(cache, moves - 1, row, col + 1);
    paths %= MOD;

    // down
    paths += dfs(cache, moves - 1, row + 1, col);
    paths %= MOD;

    cache[moves as usize][row as usize][col as usize] = paths;
    paths
}
```
