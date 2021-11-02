# [221. Maximal Square](https://leetcode.com/problems/maximal-square/)

## Problem

Given an `m x n` binary matrix filled with `0`'s and `1`'s, find the largest
square containing only `1`'s and return its area.

#### Constraints

* `m == matrix.length`
* `n == matrix[i].length`
* `1 <= m, n <= 300`
* `matrix[i][j]` is `0` or `1`

#### Examples

```text
Input: matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 4
```

```text
Input: matrix = [["0","1"],["1","0"]]
Output: 1
```

```text
Input: matrix = [["0"]]
Output: 0
```

## Solutions

### Finding maximal rectangle

This is essentially the same solution as in [84. Largest Rectangle in Histogram]
and [85. Maximal Rectangle]

```rust
pub fn maximal_square(matrix: Vec<Vec<char>>) -> i32 {
    let mut grid = vec![vec![0; matrix[0].len()]; matrix.len()];
    for r in 0..matrix.len() {
        for c in 0..matrix[0].len() {
            let mut cell = 0;

            if matrix[r][c] == '1' {
                cell = 1;

                if r > 0 {
                    cell += grid[r - 1][c];
                }
            }

            grid[r][c] = cell;
        }
    }

    // for row in grid.iter() {
    //     println!("{:?}", row);
    // }

    let mut reuse_widths = vec![0; matrix[0].len()];
    let mut stack = vec![];

    let mut max_square = 0;
    for row in grid.iter() {
        let square = largest_square(row, &mut reuse_widths, &mut stack);
        max_square = max_square.max(square);
    }

    max_square
}

fn largest_square(heights: &[i32], widths: &mut [usize], stack: &mut Vec<usize>) -> i32 {
    assert_eq!(heights.len(), widths.len());
    stack.clear();

    // find right boundary - the first smaller element to the right
    for (idx, &h) in heights.iter().enumerate() {
        while let Some(&pos) = stack.last() {
            if h >= heights[pos] {
                break;
            }

            widths[pos] = idx;
            stack.pop();
        }

        stack.push(idx);
    }

    // there are no smaller elements to the right than those
    while let Some(pos) = stack.pop() {
        widths[pos] = heights.len();
    }

    // find left boundary - the first smaller element to the left
    for (idx, &h) in heights.iter().enumerate().rev() {
        while let Some(&pos) = stack.last() {
            if h >= heights[pos] {
                break;
            }

            widths[pos] -= idx + 1;
            stack.pop();
        }

        stack.push(idx);
    }

    // there are no smaller elements to the left than those
    stack.clear();

    // println!();
    // println!("H: {:?}", heights);
    // println!("W: {:?}", widths);

    // we need to find a square, not a rectangle, so we need to
    // find the side of the square, which is the smaller of the
    // width/height
    let mut max_square = 0;
    for idx in 0..heights.len() {
        let side = heights[idx].min(widths[idx] as i32);
        max_square = max_square.max(side * side);
    }
    max_square
}
```

### DP

With this approach we try to find the maximum side of the square by checking the 
left, top and top-left cells. If any of those are `0`, then the current side can
be at most 1. If all of them are X, then we found a square with a side of `X+1`:


```rust
pub fn maximal_square(matrix: Vec<Vec<char>>) -> i32 {
    let mut dp = vec![vec![0; matrix[0].len()]; matrix.len()];

    let mut max = 0;
    for r in 0..matrix.len() {
        for c in 0..matrix[r].len() {
            if matrix[r][c] == '1' {
                let mut top = 0;
                if r > 0 {
                    top = dp[r - 1][c];
                }

                let mut left = 0;
                if c > 0 {
                    left = dp[r][c - 1];
                }

                let mut top_left = 0;
                if r > 0 && c > 0 {
                    top_left = dp[r - 1][c - 1];
                }

                dp[r][c] = top.min(left).min(top_left) + 1;
                max = max.max(dp[r][c]);
            }
        }
    }

    max
}
```

## Related problems
* [84. Largest Rectangle in Histogram]
* [85. Maximal Rectangle]

[84. Largest Rectangle in Histogram]: /000%20-%20099/84%20-%20Largest%20Rectangle%20in%20Histogram.md
[85. Maximal Rectangle]: /000%20-%20099/85%20-%20Maximal%20Rectangle.md
