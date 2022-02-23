# [240. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)

## Problem

Write an efficient algorithm that searches for a target value in an `m x n`
integer matrix. The matrix has the following properties:

* Integers in each row are sorted in ascending from left to right.
* Integers in each column are sorted in ascending from top to bottom.

#### Examples

```text
Input: matrix = [
  [1,4,7,11,15],
  [2,5,8,12,19],
  [3,6,9,16,22],
  [10,13,14,17,24],
  [18,21,23,26,30]
], target = 5
Output: true
```

```text
Input: matrix = [
  [1,4,7,11,15],
  [2,5,8,12,19],
  [3,6,9,16,22],
  [10,13,14,17,24],
  [18,21,23,26,30]
], target = 20
Output: false
```

#### Constraints

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= n, m <= 300`
* `-10^9 <= matrix[i][j] <= 10^9`
* `-10^9 <= target <= 10^9`
* All the integers in each row are sorted in ascending order.
* All the integers in each column are sorted in ascending order.

## Solutions

### Iteration + binary search

We iterate over the rows and check if the target value is between the smallest
an the largest element of the row. We then do a regular binary search:

```rust
pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
    for row in matrix.iter() {
        if row[0] > target || row[row.len() - 1] < target {
            continue;
        }

        if row.binary_search(&target).is_ok() {
            return true;
        }
    }

    false
}
```

### We traverse the matrix diagonally

We can take advantage of the problem constraints:

* All the integers in each row are sorted in ascending order.
* All the integers in each column are sorted in ascending order.

```rust
pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
    let mut width = matrix[0].len();

    // start from the smallest element of the last row
    let mut w = 0;

    // start from the last row and move towards the first row
    let mut h = matrix.len() - 1;

    while w < width {
        if target == matrix[h][w] {  //check if we've found it
            return true;
        } else if target > matrix[h][w] { // all elements on the right are larger
            w += 1;
        } else if target < matrix[h][w] { // all elements above are smaller
            if h == 0 { // there are no smaler elements
                break;
            }
            h -= 1;
        }
    }

    false
}
```

## Related problems

* [74. Search a 2D Matrix](/leetcode/000%20-%20099/74%20-%20Search%20a%202D%20Matrix.md)
