# [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

## Problem

Write an efficient algorithm that searches for a value in an `m x n` matrix.
This matrix has the following properties:

* Integers in each row are sorted from left to right.
* The first integer of each row is greater than the last integer of the previous
  row.

#### Constraints:

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= m, n <= 100`
* `-10^4 <= matrix[i][j], target <= 10^4`

#### Examples

```text
matrix = [
  [1,3,5,7],
  [10,11,16,20],
  [23,30,34,60]
], 
target = 3

Output: true
```

## Solutions

### With two binary searches

We do a binary search on the first column in order to identify the row which
might contain the target value. Then we do a binary search on that row

```rust
pub fn search_matrix(matrix: Vec<Vec<i32>>, target: i32) -> bool {
    if matrix.is_empty() {
        return false;
    }

    let mut lo = 0;
    let mut hi = matrix.len() - 1;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        if matrix[mid][0] == target {
            return true;
        }

        if matrix[mid][0] > target {
            if mid == 0 {
                break;
            }

            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }

    matrix[lo.saturating_sub(1)].binary_search(&target).is_ok()
}
```

## Related problems

* [240. Search a 2D Matrix II](/200%20-%20299/240%20-%20Search%20a%202D%20Matrix%20II.md)
