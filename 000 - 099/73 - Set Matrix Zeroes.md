# [73. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

## Problem

Given an `m x n` integer matrix `matrix`, if an element is 0, set its entire row
and column to 0's, and return the matrix.

You must do it in place.

Could you devise a constant space solution?

#### Constraints

* `m == matrix.length`
* `n == matrix[0].length`
* `1 <= m, n <= 200`
* `-2^31 <= matrix[i][j] <= 2^31 - 1`

#### Examples

```text
Input: matrix = [[1,1,1],[1,0,1],[1,1,1]]
Output: [[1,0,1],[0,0,0],[1,0,1]]
```

```text
Input: matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
Output: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```

## Solution

```rust
    pub fn set_zeroes(matrix: &mut Vec<Vec<i32>>) {
    let mut zc = Vec::with_capacity(matrix.len());

    for row in matrix.iter_mut() {
        let mut has_zero = false;

        for cn in 0..row.len() {
            if row[cn] == 0 {
                if let Err(idx) = zc.binary_search(&cn) {
                    zc.insert(idx, cn);
                }

                if !has_zero {
                    row.iter_mut().take(cn + 1).for_each(|v| *v = 0);
                    has_zero = true;
                }
            }

            if has_zero {
                row[cn] = 0;
            }
        }
    }

    for row in matrix.iter_mut() {
        for cn in zc.iter().copied() {
            row[cn] = 0;
        }
    }
}
```