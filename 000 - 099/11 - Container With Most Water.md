# [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

## Problem

Given `n` non-negative integers `a1, a2, ..., an` , where each represents a
point at coordinate `(i, ai)`. `n` vertical lines are drawn such that the two
endpoints of the line `i` is at `(i, ai)` and `(i, 0)`. Find two lines, which,
together with the x-axis forms a container, such that the container contains the
most water.

Notice that you may not slant the container.

#### Examples

```text
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

```text
Input: height = [1,1]
Output: 1
```

```text
Input: height = [4,3,2,1,4]
Output: 16
```

## Solution

```rust
    pub fn max_area(height: Vec<i32>) -> i32 {
    let mut l = 0;
    let mut r = height.len() - 1;
    let mut area = 0;

    while l < r {
        let hl = height[l];
        let hr = height[r];
        
        area = area.max((r - l) as i32 * hl.min(hr));

        if hl < hr {
            l += 1;
        } else {
            r -= 1;
        }
    }

    area
}
```