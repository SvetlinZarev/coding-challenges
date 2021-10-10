# [119. Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/)

## Problem

Given an integer rowIndex, return the `rowIndexth` (0-indexed) row of the
Pascal's triangle.

In Pascal's triangle, each number is the sum of the two numbers directly above
it

#### Constraints

* `0 <= rowIndex <= 33`

#### Examples

```text
Input: rowIndex = 3
Output: [1,3,3,1]
```

```text
Input: rowIndex = 0
Output: [1]
```

```text
Input: rowIndex = 1
Output: [1,1]
```

## Solution

```rust
pub fn get_row(row_index: i32) -> Vec<i32> {
    let mut solution = vec![0; (row_index + 1) as usize];

    solution[0] = 1;
    for r in 1..(row_index + 1) as usize {
        solution[r] = 1;

        for idx in (1..r).rev() {
            solution[idx] += solution[idx - 1];
        }
    }

    solution
}
```