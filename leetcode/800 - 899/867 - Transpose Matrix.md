# [867. Transpose Matrix](https://leetcode.com/problems/transpose-matrix/)

## Problem

### Description

Given a 2D integer array `matrix`, return the transpose of `matrix`.

The transpose of a matrix is the matrix flipped over its main diagonal,
switching the matrix's row and column indices.

```text
1  2  3         1  4  7
4  5  6   =>    2  5  8
7  8  9         3  6  9
```

#### Hints

* We don't need any special algorithms to do this. You just need to know what
  the transpose of a matrix looks like. Rows become columns and vice versa!

### Constraints

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= m, n <= 1000`
* `1 <= m * n <= 10^5`
* `-10^9 <= matrix[i][j] <= 10^9`

### Examples

```text
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[1,4,7],[2,5,8],[3,6,9]]
```

```text
Input: matrix = [[1,2,3],[4,5,6]]
Output: [[1,4],[2,5],[3,6]]
```

## Solutions

```rust
pub fn transpose(matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    if matrix.len() == 0 {
        return vec![];
    }

    // If the matrix is square, then we can avoid the unnecessary allocation
    // by transposing the matrix in-place
    if matrix.len() == matrix[0].len() {
        return transpose_square_matrix_in_place(matrix);
    }

    transpose_matrix(matrix)
}

// We can avoid the allocation if it's a square matrix
fn transpose_square_matrix_in_place(mut matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    for r in 0..matrix.len() {
        // Note that we are iteration from R instead of 0, because otherwise we will swap
        // (r,c) with (c, r), but later we we swap it again, thus undoing the first swap
        for c in r..matrix[r].len() {
            let temp = matrix[r][c];
            matrix[r][c] = matrix[c][r];
            matrix[c][r] = temp;
        }
    }

    matrix
}

// Works for any matrix, but allocates an new vector
fn transpose_matrix(matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    let transposed_columns_len = matrix.len();
    let transposed_rows_len = matrix[0].len();
    let mut transposed = vec![vec![0; transposed_columns_len]; transposed_rows_len];

    for r in 0..matrix.len() {
        for c in 0..matrix[r].len() {
            transposed[c][r] = matrix[r][c];
        }
    }

    transposed
}
```
