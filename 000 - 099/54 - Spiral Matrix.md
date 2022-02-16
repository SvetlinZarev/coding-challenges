# [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

## Problem

Given an `m x n` matrix, return all elements of the `matrix` in spiral order.

#### Constraints

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= m, n <= 10`
* `-100 <= matrix[i][j] <= 100`

#### Examples

![image](/000%20-%20099/resources/54/spiral1.jpg)

```text
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

![image](/000%20-%20099/resources/54/spiral2.jpg)

```text
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

#### Hints

* Well for some problems, the best way really is to come up with some algorithms
  for simulation. Basically, you need to simulate what the problem asks us to
  do.
* We go boundary by boundary and move inwards. That is the essential operation.
  First row, last column, last row, first column and then we move inwards by 1
  and then repeat. That's all, that is all the simulation that we need.

## Solution

```rust
pub fn spiral_order(matrix: Vec<Vec<i32>>) -> Vec<i32> {
    let mut result = Vec::with_capacity(matrix.len() * matrix[0].len());

    let mut rmin = 0;
    let mut rmax = matrix.len() - 1;

    let mut cmin = 0;
    let mut cmax = matrix[0].len() - 1;

    while rmin <= rmax && cmin <= cmax {
        // go right
        for idx in cmin..=cmax {
            result.push(matrix[rmin][idx]);
        }

        // go down
        if rmin + 1 > rmax {
            break;
        }
        for idx in rmin + 1..=rmax {
            result.push(matrix[idx][cmax]);
        }

        // go left
        if cmin == cmax {
            break;
        }
        for idx in (cmin..=cmax - 1).rev() {
            result.push(matrix[rmax][idx]);
        }

        // go up
        if rmin + 1 > rmax - 1 {
            break;
        }
        for idx in (rmin + 1..=rmax - 1).rev() {
            result.push(matrix[idx][cmin]);
        }

        rmin += 1;
        cmin += 1;
        rmax -= 1;
        cmax -= 1;
    }

    result
}
```