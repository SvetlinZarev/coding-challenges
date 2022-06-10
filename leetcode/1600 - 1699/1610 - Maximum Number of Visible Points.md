# [1610. Maximum Number of Visible Points](https://leetcode.com/problems/maximum-number-of-visible-points/)

## Problem

### Description

You are given an array `points`, an integer `angle`, and your `location`,
where `location = [posx, posy]` and `points[i] = [xi, yi]` both denote integral
coordinates on the X-Y plane.

Initially, you are facing directly east from your position. You cannot move from
your position, but you can rotate. In other words, `posx` and `posy` cannot be
changed. Your field of view in **degrees** is represented by `angle`,
determining how wide you can see from any given view direction. Let `d` be the
amount in degrees that you rotate counterclockwise. Then, your field of view is
the inclusive range of angles `[d - angle/2, d + angle/2]`.

You can see some set of points if, for each point, the angle formed by the
point, your position, and the immediate east direction from your position is in
your field of view.

There can be multiple points at one coordinate. There may be points at your
location, and you can always see these points regardless of your rotation.
Points do not obstruct your vision to other points.

Return the maximum number of points you can see.

### Constraints

* `1 <= points.length <= 10^5`
* `points[i].length == 2`
* `location.length == 2`
* `0 <= angle < 360`
* `0 <= posx, posy, xi, yi <= 100`

### Examples

#### Example 1

![image](resources/1610/ex1.png)

```text
Input: points = [[2,1],[2,2],[3,3]], angle = 90, location = [1,1]
Output: 3
```

> Explanation: The shaded region represents your field of view. All points can
> be made visible in your field of view, including `[3,3]` even though `[2,2]`
> is in front and in the same line of sight.

#### Example 2

```text
Input: points = [[2,1],[2,2],[3,4],[1,1]], angle = 90, location = [1,1]
Output: 4
```

> Explanation: All points can be made visible in your field of view, including
> the one at your location.

#### Example 3

![image](resources/1610/ex3.png)

```text
Input: points = [[1,0],[2,1]], angle = 13, location = [1,1]
Output: 1
```

> Explanation: You can only see one of the two points, as shown above.

## Solutions

### Sliding window

* Use `arctan2` to calculate the relative angle of each point to the origin
* Use a sliding window to maintain a view of X degrees
* Loop twice over the array in order to handle the case where there points near
  0 degrees and near 360 degrees

```rust
const ACCURACY: f64 = 0.000_000_000_001;

pub fn visible_points(points: Vec<Vec<i32>>, angle: i32, origin: Vec<i32>) -> i32 {
    if points.is_empty() {
        return 0;
    }

    let field_of_view = ACCURACY + angle as f64;

    let mut origin_in_points = 0;
    let mut angles_from_origin = points
        .iter()
        // Count any points that overlap the origin point and always add them to the answer
        .inspect(|x| {
            if origin[0] == x[0] && origin[1] == x[1] {
                origin_in_points += 1;
            }
        })
        // Skip any points that are the same as the origin point,
        //because they are always in the FoV regardless of the angle
        .filter(|x| origin[0] != x[0] || origin[1] != x[1])
        .map(|p| {
            let dx = (p[0] - origin[0]) as f64;
            let dy = (p[1] - origin[1]) as f64;
            dy.atan2(dx)
        })
        // Normalize the angle to be in the range [0; TAU)
        .map(|radians| (radians + std::f64::consts::TAU) % std::f64::consts::TAU)
        // Use degrees, because it's easier to visualize and debug
        .map(|radians| radians.to_degrees())
        .collect::<Vec<_>>();

    // If the list of points contained only the origin point, we can return immediately
    if angles_from_origin.is_empty() {
        return origin_in_points;
    }

    // Sort them in incresaing order in order to use the sliding window technique
    angles_from_origin.sort_unstable_by(|&a, b| a.partial_cmp(b).unwrap());

    // Sliding window "from" and "to"
    let mut l = 0;
    let mut r = 0;

    // Field of view "from" and "to"
    let mut fov_l = angles_from_origin[0];
    let mut fov_r;

    let mut answer = 0;

    // iterate twice in order to count points that are at the 0-360 degrees border
    for iter in 0..2 {
        while r < angles_from_origin.len() {
            // If we were using radians, then we should have used TAU instead of 360.0
            //
            // We need to add 2*PI on the second iteration.
            // We can do it with an IF statement like that:
            // ```rust
            //  fov_r = angles_from_origin[l] + std::f64::consts::TAU * (iter as f64);
            // ```
            // but we can also take advantage of FMA instructions which might be faster :)
            fov_r = 360.0f64.mul_add(iter as f64, angles_from_origin[r]);
            r += 1;

            while fov_r - fov_l > field_of_view {
                l = (l + 1) % angles_from_origin.len();

                // We need to add 2*PI on the second iteration, but only if L < R
                // in other words - when L has started its own second iteration
                // We can do it with an IF statement like that:
                // ```rust
                //  fov_l = angles_from_origin[l];
                //  if iter != 0 && l < r{
                //      fov_l += std::f64::consts::TAU * (iter as f64)
                //  }
                // ```
                // but we can also take advantage of FMA instructions and branch-less code :)
                let mul = ((iter != 0 && l < r) as usize * iter) as f64;
                fov_l = 360.0f64.mul_add(mul, angles_from_origin[l]);
            }

            // handle the case when we are iterating for a second time and R < L
            let r_add = angles_from_origin.len() * iter;
            let l_add = angles_from_origin.len() * iter * ((l < r) as usize);
            answer = answer.max((r + r_add) - (l + l_add));
        }

        r = 0;
    }

    answer as i32 + origin_in_points
}
```

## Resources

* [atan2 @ Wikipedia](https://en.wikipedia.org/wiki/Atan2)
* [Finding angle from coordinates](https://math.stackexchange.com/questions/1201337/finding-the-angle-between-two-points)
