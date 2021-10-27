# [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

## Problem

Given `n` non-negative integers representing an elevation map where the width of
each bar is 1, compute how much water it can trap after raining.

#### Constraints

* `n == height.length`
* `1 <= n <= 2 * 10^4`
* `0 <= height[i] <= 10^5`

#### Examples

```text
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. 
In this case, 6 units of rain water (blue section) are being trapped.
```

```text
Input: height = [4,2,0,3,2,5]
Output: 9
```

## Solution

### Observations

* we can only trap water between two non-zero "walls", so we need to have at
  least 3 elements in the input array
* we cannot trap any water at the left of the leftmost wall and cannot trap any
  water at the right of the rightmost wall
* The amount of trapped water depends on the height of the smallest of the
  left/right walls (otherwise it would fall over the wall)
* We can calculate the amount of trapped water at each index by finding the
  tallest wall on the left and on the right, then the trapped amount would
  be `min(left_wall, right_wall) - height[idx]`

### Finding leftmost & rightmost wall for each idx

```rust
pub fn trap(height: Vec<i32>) -> i32 {
    if height.len() < 3 {
        return 0;
    }

    let mut left = vec![0; height.len()];
    let mut right = vec![0; height.len()];

    // find the tallest wall on the left for each idx
    left[0] = height[0];
    for (idx, &h) in height.iter().enumerate().skip(1) {
        left[idx] = left[idx - 1].max(h);
    }

    // find the tallest wall on the right for each idx
    right[height.len() - 1] = height[height.len() - 1];
    for (idx, &h) in height.iter().enumerate().rev().skip(1) {
        right[idx] = right[idx + 1].max(h);
    }

    let mut trapped = 0;
    for idx in 0..height.len() {
        trapped += left[idx].min(right[idx]) - height[idx];
    }

    trapped
}
```

### Using 2 pointers:

```rust
pub fn trap(height: Vec<i32>) -> i32 {
    if height.len() < 3 {
        return 0;
    }

    let mut l = 0;
    let mut r = height.len() - 1;

    // track the highest wall on the left/right
    let mut lmax = height[l];
    let mut rmax = height[r];

    // Given that initially lmax == height[l], we cannot 
    // cannot trap any water on the left, so move the left 
    //pointer. This will avoid an underflow when `l==0` and
    // we do `r -= 1`
    l += 1;

    // Given that initially rmax == height[r], we cannot 
    // cannot trap any water on the right, so move the right 
    // pointer. 
    r -= 1;

    let mut trapped = 0;

    while l <= r {
        // If the left wall is higher than the right wall, then we can trap
        // at most `rmax` water. And if the right wall is higher than the left,
        // then we can trap at most `lmax` water.
        if lmax > rmax {
            if height[r] >= rmax {
                // we have a new wall that is higher than the previous maximum
                rmax = height[r];
            } else {
                // The amount of trapped water at this location is the lower of the
                // left/right walls minus the current cell height
                trapped += rmax - height[r];
            }

            // move to the next cell
            r -= 1;
        } else {
            if height[l] >= lmax {
                // we have a new wall that is higher than the previous maximum
                lmax = height[l];
            } else {
                // The amount of trapped water at this location is the lower of the
                // left/right walls minus the current cell height
                trapped += lmax - height[l];
            }

            // move to the next cell
            l += 1;
        }
    }

    trapped
}
```
