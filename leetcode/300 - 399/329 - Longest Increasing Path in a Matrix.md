# [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

## Problem

### Description

Given an `m x n` integers matrix, return the length of the longest increasing
path in `matrix`.

From each cell, you can either move in four directions: left, right, up, or
down. You **may not** move diagonally or move outside the boundary (i.e.,
wrap-around is not allowed).

### Constraints

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= m, n <= 200`
* `0 <= matrix[i][j] <= 2^31 - 1`

### Examples

#### Example 1

![image](resources/329/ex1.jpg)

```text
Input: matrix = [[9,9,4],[6,6,8],[2,1,1]]
Output: 4
Explanation: The longest increasing path is [1, 2, 6, 9].
```

#### Example 2

![image](resources/329/ex2.jpg)

```text
Input: matrix = [[3,4,5],[3,2,6],[2,2,1]]
Output: 4
Explanation: The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
```

#### Example 3

```text
Input: matrix = [[1]]
Output: 1
```

## Solutions

### DFS/Bruteforce (Time Limit Exceeded)

```rust
pub fn longest_increasing_path(matrix: Vec<Vec<i32>>) -> i32 {
    let mut answer = 0;
    for r in 0..matrix.len() {
        for c in 0..matrix[r].len() {
            answer = answer.max(dfs(&matrix, r, c, matrix[r][c]));
        }
    }

    answer
}

fn dfs(matrix: &[Vec<i32>], r: usize, c: usize, value: i32) -> i32 {
    let mut len = 0;

    if r > 0 && matrix[r - 1][c] > value {
        len = len.max(dfs(matrix, r - 1, c, matrix[r - 1][c]));
    }

    if c > 0 && matrix[r][c - 1] > value {
        len = len.max(dfs(matrix, r, c - 1, matrix[r][c - 1]));
    }

    if c < matrix[r].len() - 1 && matrix[r][c + 1] > value {
        len = len.max(dfs(matrix, r, c + 1, matrix[r][c + 1]));
    }

    if r < matrix.len() - 1 && matrix[r + 1][c] > value {
        len = len.max(dfs(matrix, r + 1, c, matrix[r + 1][c]));
    }

    len + 1
}
```

### DFS with memoization

* Because we are interested only in cells with values larger than the value of
  the current cell, we don't need a *visited* array.

* Because we need to traverse all cells, it's better to use a vector instead of
  HashMap for the cache (i.e. the same amount of elements, direct indexing in
  both cases, but on hashcode calculation)

```rust
pub fn longest_increasing_path(matrix: Vec<Vec<i32>>) -> i32 {
    if matrix.is_empty() {
        return 0;
    }

    let mut cache = vec![vec![0; matrix[0].len()]; matrix.len()];
    let mut answer = 0;

    for r in 0..matrix.len() {
        for c in 0..matrix[r].len() {
            answer = answer.max(dfs(&matrix, &mut cache, r, c, matrix[r][c]));
        }
    }

    answer
}

fn dfs(matrix: &[Vec<i32>], cache: &mut [Vec<i32>], r: usize, c: usize, value: i32) -> i32 {
    if cache[r][c] != 0 {
        return cache[r][c];
    }

    let mut len = 0;

    if r > 0 && matrix[r - 1][c] > value {
        len = len.max(dfs(matrix, cache, r - 1, c, matrix[r - 1][c]));
    }

    if c > 0 && matrix[r][c - 1] > value {
        len = len.max(dfs(matrix, cache, r, c - 1, matrix[r][c - 1]));
    }

    if c < matrix[r].len() - 1 && matrix[r][c + 1] > value {
        len = len.max(dfs(matrix, cache, r, c + 1, matrix[r][c + 1]));
    }

    if r < matrix.len() - 1 && matrix[r + 1][c] > value {
        len = len.max(dfs(matrix, cache, r + 1, c, matrix[r + 1][c]));
    }

    cache[r][c] = len + 1;

    len + 1
}
```