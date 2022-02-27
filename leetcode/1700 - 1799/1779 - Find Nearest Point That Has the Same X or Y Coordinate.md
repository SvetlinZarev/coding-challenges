# [1779. Find Nearest Point That Has the Same X or Y Coordinate](https://leetcode.com/problems/find-nearest-point-that-has-the-same-x-or-y-coordinate/)

## Problem

### Description

You are given two integers, `x` and `y`, which represent your current location
on a Cartesian grid: `(x, y)`. You are also given an array points where
each `points[i] = [ai, bi]` represents that a point exists at `(ai, bi)`. A
point is valid if it shares the same `x`-coordinate or the same `y`-coordinate
as your location.

Return the index (0-indexed) of the **valid** point with the smallest
**Manhattan distance** from your current location. If there are multiple, return
the valid point with **the smallest** index. If there are no valid points,
return `-1`.

**Note:** The Manhattan distance between two points `(x1, y1)` and `(x2, y2)`
is `abs(x1 - x2) + abs(y1 - y2)`.

### Constraints

* `1 <= points.length <= 10^4`
* `points[i].length == 2`
* `1 <= x, y, ai, bi <= 10^4`

### Examples

```text
Input: x = 3, y = 4, points = [[1,2],[3,1],[2,4],[2,3],[4,4]]
Output: 2
Explanation: Of all the points, only [3,1], [2,4] and [4,4] are valid. Of the valid points, [2,4] and [4,4] have the smallest Manhattan distance from your current location, with a distance of 1. [2,4] has the smallest index, so return 2.
```

```text
Input: x = 3, y = 4, points = [[3,4]]
Output: 0
Explanation: The answer is allowed to be on the same location as your current location.
```

```text
Input: x = 3, y = 4, points = [[2, 3]]
Output: - 1
Explanation: There are no valid points.
```

## Solutions

### With iterators

```rust
pub fn nearest_valid_point(x: i32, y: i32, points: Vec<Vec<i32>>) -> i32 {
    points
        .iter()
        .enumerate()
        .filter(|&(_idx, p)| p[0] == x || p[1] == y)
        .min_by(|&(_, a), &(_, b)| {
            let m1 = manhattan((x, y), (a[0], a[1]));
            let m2 = manhattan((x, y), (b[0], b[1]));
            m1.cmp(&m2)
        })
        .map(|(idx, _)| idx as i32)
        .unwrap_or(-1)
}

fn manhattan(loc: (i32, i32), point: (i32, i32)) -> i32 {
    (loc.0 - point.0).abs() + (loc.1 - point.1).abs()
}
```

### With a loop

```rust
pub fn nearest_valid_point(x: i32, y: i32, points: Vec<Vec<i32>>) -> i32 {
    let mut nearest = None;
    let mut distance = None;

    for (idx, p) in points.into_iter().enumerate() {
        if x == p[0] || y == p[1] {
            let manhattan = manhattan((x, y), (p[0], p[1]));
            if distance.is_none() || Some(manhattan) < distance {
                distance = Some(manhattan);
                nearest = Some(idx as i32);
            }
        }
    }

    nearest.unwrap_or(-1)
}

fn manhattan(loc: (i32, i32), point: (i32, i32)) -> i32 {
    (loc.0 - point.0).abs() + (loc.1 - point.1).abs()
}
```