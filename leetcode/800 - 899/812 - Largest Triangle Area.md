# [812. Largest Triangle Area](https://leetcode.com/problems/largest-triangle-area/)

## Problem

### Description

Given an array of points on the `X-Y` plane points where `points[i] = [xi, yi]`,
return the area of the largest triangle that can be formed by any three
different points. Answers within `10^-5` of the actual answer will be accepted.

### Constraints

* `3 <= points.length <= 50`
* `-50 <= xi, yi <= 50`
* All the given points are unique.

### Examples

```text
Input: points = [[0,0],[0,1],[1,0],[0,2],[2,0]]
Output: 2.00000
```

```text
Input: points = [[1,0],[0,0],[0,1]]
Output: 0.50000
```

## Solutions

### Bruteforce

Because the number of points is low, we can try a bruteforce approach

```rust
pub fn largest_triangle_area(points: Vec<Vec<i32>>) -> f64 {
    let mut max_area = 0.0f64;

    for i in 0..points.len() {
        let p0 = &points[i];

        for j in i + 1..points.len() {
            let p1 = &points[j];

            for k in j + 1..points.len() {
                let p2 = &points[k];

                let a = dist(p0[0], p0[1], p1[0], p1[1]);
                let b = dist(p2[0], p2[1], p1[0], p1[1]);
                let c = dist(p0[0], p0[1], p2[0], p2[1]);

                max_area = max_area.max(area(a, b, c));
            }
        }
    }

    max_area
}

fn dist(x0: i32, y0: i32, x1: i32, y1: i32) -> f64 {
    let d = (x0 - x1).pow(2) + (y0 - y1).pow(2);
    (d as f64).sqrt()
}

fn area(a: f64, b: f64, c: f64) -> f64 {
    // Heron's formula
    let s = (a + b + c) / 2.0;
    let a = s * (s - a) * (s - b) * (s - c);
    a.sqrt()
}
```

## Resources

* [Heron's formula](https://en.wikipedia.org/wiki/Heron%27s_formula)
* [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance)

## Related Problems

* [976. Largest Perimeter Triangle](/leetcode/900%20-%20999/976%20-%20Largest%20Perimeter%20Triangle.md)