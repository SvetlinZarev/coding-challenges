# [1351. Count Negative Numbers in a Sorted Matrix](https://leetcode.com/problems/count-negative-numbers-in-a-sorted-matrix/)

## Problem

### Description

Given a `m x n` matrix grid which is sorted in non-increasing order both
row-wise and column-wise, return the number of negative numbers in `grid`.

**Follow up:** Could you find an `O(n + m)` solution?

### Constraints

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 100`
* `-100 <= grid[i][j] <= 100`

### Examples

```text
Input: grid = [[4,3,2,-1],[3,2,1,-1],[1,1,-1,-2],[-1,-1,-2,-3]]
Output: 8
Explanation: There are 8 negatives number in the matrix.
```

```text
Input: grid = [[3,2],[1,0]]
Output: 0
```

## Solutions

## O(m + n)

Because the grid is sorted in non-increasing way:

* we can start from column 0 and move right, because no column on the left on
  any upper row will be smaller than this one
* we can start from the last row, because no upper row will be smaller than this
  one

```rust
pub fn count_negatives(grid: Vec<Vec<i32>>) -> i32 {
    assert!(grid.len() > 0);
    assert!(grid[0].len() > 0);

    let cols = grid[0].len();
    let mut r = grid.len() - 1;
    let mut c = 0;

    let mut count = 0;
    loop {
        while c < cols && grid[r][c] >= 0 {
            c += 1;
        }

        if c >= cols {
            break;
        }

        count += cols - c;

        if r == 0 {
            break;
        }
        r -= 1;
    }

    count as i32
}
```

## Related Problems

* [240. Search a 2D Matrix II](/leetcode/200%20-%20299/240%20-%20Search%20a%202D%20Matrix%20II.md)
