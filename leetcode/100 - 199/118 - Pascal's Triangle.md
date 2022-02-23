# [118. Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)

## Problem

Given an integer `numRows`, return the first numRows of Pascal's triangle.

In Pascal's triangle, each number is the sum of the two numbers directly above
it

#### Constraints

* `1 <= numRows <= 30`

#### Examples

```text


Input: numRows = 1
Output: [[1]]

```

```text
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

## Solution

```rust
pub fn generate(num_rows: i32) -> Vec<Vec<i32>> {
    let mut result = Vec::with_capacity(num_rows as usize);

    let mut prev = vec![1];
    for _ in 1..num_rows {
        let mut row = Vec::with_capacity(prev.len() + 1);
        row.push(1);
        for idx in 1..prev.len() {
            let value = prev[idx - 1] + prev[idx];
            row.push(value);
        }
        row.push(1);

        std::mem::swap(&mut prev, &mut row);
        result.push(row);
    }

    result.push(prev);
    result
}
```

## Related Problems

* [119. Pascal's Triangle II](119%20-%20Pascal%27s%20Triangle%20II.md)
