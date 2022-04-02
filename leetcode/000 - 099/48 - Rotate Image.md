# [48. Rotate Image](https://leetcode.com/problems/rotate-image/)

## Problem

### Description

You are given an `n x n` 2D matrix representing an image, rotate the image by 90
degrees (clockwise).

You have to rotate the image in-place, which means you have to modify the input
2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

### Constraints

* `n == matrix.length == matrix[i].length`
* `1 <= n <= 20`
* `-1000 <= matrix[i][j] <= 1000`

### Examples

#### Example 1

![image](resources/48/ex1.jpg)

```text
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```

#### Example 2

![image](resources/48/ex2.jpg)

```text
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

## Solutions

### Using matrix operations (transpose + reflect)

```rust
pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
    transpose(matrix);
    reflect(matrix);
}

fn transpose(m: &mut Vec<Vec<i32>>) {
    for r in 0..m.len() {
        for c in r + 1..m.len() {
            let tmp = m[r][c];
            m[r][c] = m[c][r];
            m[c][r] = tmp;
        }
    }
}

fn reflect(m: &mut Vec<Vec<i32>>) {
    for row in m {
        row.reverse();
    }
}
```

### Moving 4 cells at a time

```rust
pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
    let n = matrix.len();

    for i in 0..(n + 1) / 2 {
        for j in 0..n / 2 {
            let temp = matrix[n - 1 - j][i];
            matrix[n - 1 - j][i] = matrix[n - 1 - i][n - j - 1];
            matrix[n - 1 - i][n - j - 1] = matrix[j][n - 1 - i];
            matrix[j][n - 1 - i] = matrix[i][j];
            matrix[i][j] = temp;
        }
    }
}
```