# [149. Max Points on a Line](https://leetcode.com/problems/max-points-on-a-line/)

## Problem

### Description

Given an array of points where `points[i] = [xi, yi]` represents a point on
the `X-Y` plane, return the maximum number of points that lie on the same
straight line.

### Constraints

* `1 <= points.length <= 300`
* `points[i].length == 2`
* `-10^4 <= xi, yi <= 10^4`
* All the points are unique.

### Examples

#### Example 1

![image](resources/149/ex1.jpg)

```text
Input: points = [[1,1],[2,2],[3,3]]
Output: 3
```

#### Example 2

![image](resources/149/ex2.jpg)

```text
Input: points = [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
```

## Solutions

### Bruteforce

```rust
pub fn max_points(points: Vec<Vec<i32>>) -> i32 {
    let mut answer = points.len().min(2);

    for i in 0..points.len().saturating_sub(2) {
        let x1 = points[i][0];
        let y1 = points[i][1];

        for j in i + 1..points.len().saturating_sub(1) {
            let x2 = points[j][0];
            let y2 = points[j][1];

            let mut current = 0;

            for k in j + 1..points.len() {
                let x3 = points[k][0];
                let y3 = points[k][1];

                if collinear(x1, y1, x2, y2, x3, y3) {
                    current += 1;
                }
            }

            answer = answer.max(current + 2);
        }
    }

    answer as i32
}

// The simplest way to check if 3 points are collinear is to
// check if they form a triangle. This can be done by using
// the Heron's formula: `s^2 = p(p-a)(p-b)(p-c)`. Where `a`,
// `b`, `c` are the lengths of the triangle's sides. And `p`
// is 1/2 of the perimeter. If the area  of the triangle is 0,
// then the points are collinear
//
// But calculating the distance from the cartesian coordinates
// involves using a square root: `d = sqrt((x2 - x1)^2 + (y2 - y1)^2)`
// which will lead to using floating point numbers which will elad to
// rounding errors.
//
// Instead we can use [Menelaus's theorem](https://en.wikipedia.org/wiki/Menelaus%27s_theorem)
// which allows us to avoid floats and also requires less operations
fn collinear(x1: i32, y1: i32, x2: i32, y2: i32, x3: i32, y3: i32) -> bool {
    (y1 - y2) * (x1 - x3) == (y1 - y3) * (x1 - x2)
}
```

## Resources

* [Menelaus's theorem](https://en.wikipedia.org/wiki/Menelaus%27s_theorem)
* [Heron's formula](https://en.wikipedia.org/wiki/Heron's_formula)
