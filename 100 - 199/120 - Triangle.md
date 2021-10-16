# [120. Triangle](https://leetcode.com/problems/triangle/)

## Problem

Given a triangle array, return the minimum path sum from top to bottom.

For each step, you may move to an adjacent number of the row below. More
formally, if you are on index `i` on the current row, you may move to either
index `i` or index `i + 1` on the next row.

#### Constraints

* `1 <= triangle.length <= 200`
* `triangle[0].length == 1`
* `triangle[i].length == triangle[i - 1].length + 1`
* `-10^4 <= triangle[i][j] <= 10^4`

#### Examples

```text
Input: triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]
Output: 11
Explanation: The triangle looks like:
   2
  3 4
 6 5 7
4 1 8 3
The minimum path sum from top to bottom is 2 + 3 + 5 + 1 = 11 (underlined above).
```

## Solution

```rust
pub fn minimum_total(mut triangle: Vec<Vec<i32>>) -> i32 {
    for row in (0..triangle.len()).rev().skip(1) {
        for idx in 0..triangle[row].len() {
            triangle[row][idx] += triangle[row + 1][idx].min(triangle[row + 1][idx + 1]);
        }
    }

    triangle[0][0]
}
```