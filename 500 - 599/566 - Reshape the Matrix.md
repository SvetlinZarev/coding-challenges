# [566. Reshape the Matrix](https://leetcode.com/problems/reshape-the-matrix/)

## Problem

In MATLAB, there is a handy function called `reshape` which can reshape
an `m x n`
matrix into a new one with a different size `r x c` keeping its original data.

You are given an `m x n` matrix `mat` and two integers `r` and `c` representing
the number of rows and the number of columns of the wanted reshaped matrix.

The reshaped matrix should be filled with all the elements of the original
matrix in the same row-traversing order as they were.

If the reshape operation with given parameters is possible and legal, output the
new reshaped matrix; Otherwise, output the original matrix.

#### Constraints

* `m == mat.length`
* `n == mat[i].length`
* `1 <= m, n <= 100`
* `-1000 <= mat[i][j] <= 1000`
* `1 <= r, c <= 300`

#### Examples

```text
Input: mat = [[1,2],[3,4]], r = 1, c = 4
Output: [[1,2,3,4]]
```

```text
Input: mat = [[1,2],[3,4]], r = 2, c = 4
Output: [[1,2],[3,4]]
```

## Solution

```rust
pub fn matrix_reshape(mat: Vec<Vec<i32>>, r: i32, c: i32) -> Vec<Vec<i32>> {
    let r0 = mat.len();
    if r0 == 0 {
        return mat;
    }

    let c0 = mat[0].len();
    if r0 * c0 != (r * c) as usize {
        return mat;
    }

    let mut m = vec![vec![0; c as usize]; r as usize];
    for idx in 0..r0 * c0 {
        m[idx / c as usize][idx % c as usize] = mat[idx / c0][idx % c0];
    }
    m
}
```

Another idea is to convert the 2D index into 1D index, then back to 2D index
with the new row/col.