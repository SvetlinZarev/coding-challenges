# [1572. Matrix Diagonal Sum](https://leetcode.com/problems/matrix-diagonal-sum/)

## Problem

### Description

Given a square matrix `mat`, return the sum of the matrix diagonals.

Only include the sum of all the elements on the primary diagonal and all the
elements on the secondary diagonal that are not part of the primary diagonal.

### Constraints

* `n == mat.length == mat[i].length`
* `1 <= n <= 100`
* `1 <= mat[i][j] <= 100`

### Examples

```text
Input: mat = [[1,2,3],
              [4,5,6],
              [7,8,9]]
Output: 25
Explanation: Diagonals sum: 1 + 5 + 9 + 3 + 7 = 25
Notice that element mat[1][1] = 5 is counted only once.
```

```text
Input: mat = [[1,1,1,1],
              [1,1,1,1],
              [1,1,1,1],
              [1,1,1,1]]
Output: 8
```

### Hints

#### Hint 1

> There will be overlap of elements in the primary and secondary
> diagonals if and only if the length of the matrix is odd, which
> is at the center.

## Solutions

```rust
pub fn diagonal_sum(mat: Vec<Vec<i32>>) -> i32 {
    let n = mat.len();
    if n == 1 {
        return mat[0][0];
    }

    let mut sum = match n % 2 == 0 {
        true => 0,
        false => -mat[n / 2][n / 2],
    };

    for idx in 0..n {
        sum += mat[idx][idx] + mat[idx][n - 1 - idx];
    }

    sum
}
```

```rust
pub fn diagonal_sum(mat: Vec<Vec<i32>>) -> i32 {
    let mut i = 0;
    let mut j = mat.len() - 1;

    let mut sum = 0;
    for _ in 0..mat.len() {
        sum += mat[i][i];
        if i != j {
            sum += mat[i][j];
        }

        i += 1;
        j -= 1;
    }

    sum
}
```