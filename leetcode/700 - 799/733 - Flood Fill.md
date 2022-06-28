# [733. Flood Fill](https://leetcode.com/problems/flood-fill/)

## Problem

### Description

An image is represented by an `m x n` integer grid image where `image[i][j]`
represents the pixel value of the image.

You are also given three integers `sr`, `sc`, and `newColor`. You should perform
a flood fill on the image starting from the pixel `image[sr][sc]`.

To perform a flood fill, consider the starting pixel, plus any pixels connected
4-directionally to the starting pixel of the same color as the starting pixel,
plus any pixels connected 4-directionally to those pixels (also with the same
color), and so on. Replace the color of all of the aforementioned pixels
with `newColor`.

Return the modified image after performing the flood fill.

### Constraints

* `m == image.length`
* `n == image[i].length`
* `1 <= m, n <= 50`
* `0 <= image[i][j]`, `newColor < 216`
* `0 <= sr < m`
* `0 <= sc < n`

### Examples

#### Example 1

![image](resources/733/ex1.jpg)

```text
Input: image = [[1,1,1],[1,1,0],[1,0,1]], sr = 1, sc = 1, newColor = 2
Output: [[2,2,2],[2,2,0],[2,0,1]]
```

> **Explanation:** From the center of the image with
> position `(sr, sc) = (1, 1)` ( i.e., the red pixel), all pixels connected by a
> path of the same color as the starting pixel (i.e., the blue pixels) are
> colored with the new color.
>
> **Note** the bottom corner is not colored 2, because it is not 4-directionally
> connected to the starting pixel.

#### Example 2

```text
Input: image = [[0,0,0],[0,0,0]], sr = 0, sc = 0, color = 0
Output: [[0,0,0],[0,0,0]]
```

> **Explanation:** The starting pixel is already colored 0, so no changes are
> made to the image.

## Solutions

### DFS

```rust

pub fn flood_fill(mut image: Vec<Vec<i32>>, sr: i32, sc: i32, new_color: i32) -> Vec<Vec<i32>> {
    let old_color = image[sr as usize][sc as usize];
    if old_color == new_color {
        return image;
    }

    let mut stack = Vec::with_capacity(32);
    stack.push((sr as usize, sc as usize));

    while let Some((r, c)) = stack.pop() {
        // left
        if c > 0 {
            let lc = c - 1;
            if image[r][lc] == old_color {
                stack.push((r, lc));
            }
        }

        //center
        image[r][c] = new_color;

        //right
        if c < image[r].len() - 1 {
            let rc = c + 1;
            if image[r][rc] == old_color {
                stack.push((r, rc));
            }
        }

        //top
        if r > 0 {
            let tr = r - 1;
            if image[tr][c] == old_color {
                stack.push((tr, c));
            }
        }

        //bottom
        if r < image.len() - 1 {
            let br = r + 1;
            if image[br][c] == old_color {
                stack.push((br, c));
            }
        }
    }

    image
}
```
