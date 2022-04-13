#[59. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)

## Problem

### Description

Given a positive integer `n`, generate an `n x n` matrix filled with elements
from `1` to `n^2` in spiral order.

![image](resources/59/ex1.jpg)

### Constraints

*`1 <= n <= 20`

### Examples

```text
Input: n = 3
Output: [[1,2,3],[8,9,4],[7,6,5]]
```

```text
Input: n = 1
Output: [[1]]
```

## Solutions

### Simulation v1

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum Direction {
    Right,
    Left,
    Up,
    Down,
}

pub fn generate_matrix(n: i32) -> Vec<Vec<i32>> {
    assert!(n > 0);

    let mut min_r = 0;
    let mut min_c = 0;
    let mut max_r = (n - 1) as usize;
    let mut max_c = (n - 1) as usize;

    let mut direction = Direction::Right;
    let mut r = min_r;
    let mut c = min_r;

    let mut answer = vec![vec![0; n as usize]; n as usize];

    for i in 1..=n * n {
        answer[r][c] = i;

        match direction {
            Direction::Right => {
                if c < max_c {
                    c += 1;
                } else {
                    r += 1;
                    min_r += 1;
                    direction = Direction::Down;
                }
            }
            Direction::Left => {
                if c > min_c {
                    c -= 1;
                } else {
                    r -= 1;
                    max_r -= 1;
                    direction = Direction::Up;
                }
            }
            Direction::Up => {
                if r > min_r {
                    r -= 1;
                } else {
                    c += 1;
                    min_c += 1;
                    direction = Direction::Right;
                }
            }
            Direction::Down => {
                if r < max_r {
                    r += 1;
                } else {
                    c -= 1;
                    max_c -= 1;
                    direction = Direction::Left;
                }
            }
        }
    }
    answer
}
```

### Simulation v2

```rust
pub fn generate_matrix(n: i32) -> Vec<Vec<i32>> {
    assert!(n > 0);

    let (mut l, mut r) = (0usize, (n - 1) as usize);
    let (mut t, mut b) = (0usize, (n - 1) as usize);
    let mut value = 1;
    let mut answer = vec![vec![0; n as usize]; n as usize];

    while l <= r && t <= b {
        // go right
        for idx in l..=r {
            answer[t][idx] = value;
            value += 1;
        }
        t += 1;

        // go down
        for idx in t..=b {
            answer[idx][r] = value;
            value += 1;
        }
        if r == 0 {
            break;
        }
        r -= 1;

        // go left
        for idx in (l..=r).rev() {
            answer[b][idx] = value;
            value += 1;
        }
        if b == 0 {
            break;
        }
        b -= 1;

        // go up
        for idx in (t..=b).rev() {
            answer[idx][l] = value;
            value += 1;
        }
        l += 1;
    }

    answer
}
```

## Related Problems

* [54. Spiral Matrix](54%20-%20Spiral%20Matrix.md)
