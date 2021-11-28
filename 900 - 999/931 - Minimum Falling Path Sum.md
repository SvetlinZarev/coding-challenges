# [931. Minimum Falling Path Sum](https://leetcode.com/problems/minimum-falling-path-sum/)

## Problem

Given an `n x n` array of integers `matrix`, return the minimum sum of any
falling path through `matrix`.

A falling path starts at any element in the first row and chooses the element in
the next row that is either directly below or diagonally left/right.
Specifically, the next element from position `(row, col)` will
be `(row + 1, col - 1)`, `(row + 1, col)`, or `(row + 1, col + 1)`.

#### Constraints

* `n == matrix.length == matrix[i].length`
* `1 <= n <= 100`
* `-100 <= matrix[i][j] <= 100`

#### Examples

![grid](resources/931/failing1-grid.jpg)

```text
Input: matrix = [[2,1,3],[6,5,4],[7,8,9]]
Output: 13
Explanation: There are two falling paths with a minimum sum as shown.
```

![grid](resources/931/failing2-grid.jpg)

```text
Input: matrix = [[-19,57],[-40,-5]]
Output: -59
Explanation: The falling path with a minimum sum is shown.
```

## Solution

```rust
pub fn min_falling_path_sum(mut matrix: Vec<Vec<i32>>) -> i32 {
    for r in (0..matrix.len()).rev().skip(1) {
        for c in 0..matrix[r].len() {
            let mut min = matrix[r + 1][c];
            if c > 0 {
                min = min.min(matrix[r + 1][c - 1]);
            }
            if c < matrix[r].len() - 1 {
                min = min.min(matrix[r + 1][c + 1]);
            }

            matrix[r][c] += min;
        }
    }

    matrix[0].iter().copied().min().unwrap()
}
```