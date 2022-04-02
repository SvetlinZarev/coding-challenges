# [1886. Determine Whether Matrix Can Be Obtained By Rotation](https://leetcode.com/problems/determine-whether-matrix-can-be-obtained-by-rotation/)

## Problem

### Description

Given two `n x n` binary matrices `mat` and `target`, return `true` if it is
possible to make mat equal to target by rotating mat in 90-degree increments,
or `false` otherwise

### Constraints

* `n == mat.length == target.length`
* `n == mat[i].length == target[i].length`
* `1 <= n <= 10`
* `mat[i][j]` and `target[i][j]` are either `0` or `1`.

### Examples

#### Example 1

![image](resources/1886/ex1.png)

```text
Input: mat = [[0,1],[1,0]], target = [[1,0],[0,1]]
Output: true
Explanation: We can rotate mat 90 degrees clockwise to make mat equal target.
```

#### Example 2

![image](resources/1886/ex2.png)

```text
Input: mat = [[0,1],[1,1]], target = [[1,0],[0,1]]
Output: false
Explanation: It is impossible to make mat equal to target by rotating mat.
```

#### Example 3

![image](resources/1886/ex3.png)

```text
Input: mat = [[0,0,0],[0,1,0],[1,1,1]], target = [[1,1,1],[0,1,0],[0,0,0]]
Output: true
Explanation: We can rotate mat 90 degrees clockwise two times to make mat equal target.
```

## Solutions

```rust
const ROT_0: u32 = 0;
const ROT_1: u32 = 1;
const ROT_2: u32 = 2;
const ROT_3: u32 = 3;

pub fn find_rotation(mat: Vec<Vec<i32>>, target: Vec<Vec<i32>>) -> bool {
    assert_eq!(mat.len(), target.len());

    let n = mat.len();
    let mut rotations = 1 << ROT_0 | 1 << ROT_1 | 1 << ROT_2 | 1 << ROT_3;

    for i in 0..n {
        for j in 0..n {
            if mat[i][j] != target[i][j] {
                rotations &= !(1 << ROT_0);
            }

            if mat[i][j] != target[n - 1 - j][i] {
                rotations &= !(1 << ROT_1);
            }

            if mat[i][j] != target[n - 1 - i][n - 1 - j] {
                rotations &= !(1 << ROT_2);
            }

            if mat[i][j] != target[j][n - 1 - i] {
                rotations &= !(1 << ROT_3);
            }

            // an early exit, maybe
            if rotations == 0 {
                return false;
            }
        }
    }

    rotations != 0
}
```

## Related Problems

* [48. Rotate Image](/leetcode/000%20-%20099/48%20-%20Rotate%20Image.md)