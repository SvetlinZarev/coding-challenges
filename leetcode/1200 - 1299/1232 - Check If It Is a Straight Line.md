# [1232. Check If It Is a Straight Line](https://leetcode.com/problems/check-if-it-is-a-straight-line/)

## Problem

### Description

You are given an array `coordinates`,` coordinates[i] = [x, y]`, where `[x, y]`
represents the coordinate of a point. Check if these points make a straight line
in the XY plane.

### Constraints

* `2 <= coordinates.length <= 1000`
* `coordinates[i].length == 2`
* `-10^4 <= coordinates[i][0], coordinates[i][1] <= 10^4`
* `coordinates` contains no duplicate point.

### Examples

![image](/leetcode/1200%20-%201299/resources/1232/ex1.jpg)

```text
Input: coordinates = [[1,2],[2,3],[3,4],[4,5],[5,6],[6,7]]
Output: true
```

![image](/leetcode/1200%20-%201299/resources/1232/ex2.jpg)

```text
Input: coordinates = [[1,1],[2,2],[3,4],[4,5],[5,6],[7,7]]
Output: false
```

## Solution

### Cross product

```rust
pub fn check_straight_line(coordinates: Vec<Vec<i32>>) -> bool {
    if coordinates.len() <= 2 {
        return coordinates.len() == 2;
    }

    coordinates
        .windows(3)
        .map(|w| (&w[0], &w[1], &w[2]))
        .map(|(a, b, c)| ((b[1] - a[1]) * (c[0] - a[0]) == (b[0] - a[0]) * (c[1] - a[1])))
        .fold(true, |acc, val| acc & val)
}
```

## Resources

* [Stack Exchange](https://math.stackexchange.com/questions/701862/how-to-find-if-the-points-fall-in-a-straight-line-or-not)
* [Wolfram MathWorld](https://mathworld.wolfram.com/Collinear.html)