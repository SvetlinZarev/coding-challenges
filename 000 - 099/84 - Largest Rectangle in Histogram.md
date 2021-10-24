# [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

## Problem

Given an array of integers heights representing the histogram's bar height where
the width of each bar is `1`, return the area of the largest rectangle in the
histogram.

#### Constraints

* `1 <= heights.length <= 10^5`
* `0 <= heights[i] <= 10^4`

#### Examples

```text
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The above is a histogram where width of each bar is 1.
The largest rectangle is shown in the red area, which has an area = 10 units.
```

```text
Input: heights = [2,4]
Output: 4
```

## Solution

### Using an increasing stack

The idea is to be able to find the "next smaller" element for each array element
in a linear time. We do this once in a left-to-right direction and once in a
right-to-left direction in order to find the left & right boundaries of the
rectangle.

```rust
pub fn largest_rectangle_area(heights: Vec<i32>) -> i32 {
    let mut widths = vec![(0, heights.len()); heights.len()];
    let mut stack = vec![];

    // find the next smaller element (i.e. the right boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            widths[pos] = (0, idx);
            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from left to right, and there are
    // no smaller elements than those in the stack. So their right boundary
    // is the array length, but as we've already initialized it with that
    // value we have nothing to do now.
    stack.clear();

    // find the previous smaller element (i.e. the left boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate().rev() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            // `idx` contains the smaller element, but the start index needs
            // to be equal or larger, that's why the left boundary is `idx + 1`
            widths[pos].0 = idx + 1;
            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from right to left, and there are
    // no smaller elements than those in the stack. So their left boundary
    // is the beginning of the array, but as we've already initialized it with
    // that value we have nothing to do now.
    stack.clear();

    let mut max_area = 0;
    for (idx, (left, right)) in widths.into_iter().enumerate() {
        let area = heights[idx] * (right - left) as i32;
        max_area = max_area.max(area);
    }

    max_area
}
```

We can improve on the memory usage by storing only the `width` instead
if `left, right`. On the first pass we store the right boundary, and on the
second pass we subtract the left boundary, thus getting the width

```rust
pub fn largest_rectangle_area(heights: Vec<i32>) -> i32 {
    let mut widths = vec![heights.len(); heights.len()];
    let mut stack: Vec<usize> = vec![];

    // find the next smaller element (i.e. the right boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            widths[pos] = idx;
            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from left to right, and there are
    // no smaller elements than those in the stack. So their right boundary
    // is the array length, but as we've already initialized it with that
    // value we have nothing to do now.
    stack.clear();

    // find the previous smaller element (i.e. the left boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate().rev() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            // `idx` contains the smaller element, but the start index needs
            // to be equal or larger, that's why the left boundary is `idx + 1`
            //
            // So we can finally compute the width by subtracting `idx+1` from
            // the already stored right boundary
            widths[pos] -= idx + 1;
            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from right to left, and there are
    // no smaller elements than those in the stack. So their left boundary
    // is the beginning of the array, which is 0, so we don't need to do
    // anything else for them
    stack.clear();

    let mut max_area = 0;
    for (idx, width) in widths.into_iter().enumerate() {
        let area = heights[idx] * width as i32;
        max_area = max_area.max(area);
    }

    max_area
}
```

And after our `widths` vector contains the width of each rectangle, we can 
calculate the area of the rectangle directly in the second loop:

```rust
pub fn largest_rectangle_area(heights: Vec<i32>) -> i32 {
    let mut widths = vec![heights.len(); heights.len()];
    let mut stack: Vec<usize> = vec![];

    // find the next smaller element (i.e. the right boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            widths[pos] = idx;
            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from left to right, and there are
    // no smaller elements than those in the stack. So their right boundary
    // is the array length, but as we've already initialized it with that
    // value we have nothing to do now.
    stack.clear();

    let mut max_area = 0;

    // find the previous smaller element (i.e. the left boundary of the rectangle)
    for (idx, &x) in heights.iter().enumerate().rev() {
        while let Some(&pos) = stack.last() {
            if x >= heights[pos] {
                break;
            }

            // `idx` contains the smaller element, but the start index needs
            // to be equal or larger, that's why the left boundary is `idx + 1`
            //
            // So we can finally compute the width by subtracting `idx+1` from
            // the already stored right boundary
            let area = (widths[pos] - (idx + 1)) as i32 * heights[pos];
            max_area = max_area.max(area);

            stack.pop();
        }

        stack.push(idx);
    }

    // We've processed the whole array from right to left, and there are
    // no smaller elements than those in the stack. So their left boundary
    // is the beginning of the array, i.e. 0, so compute the `max_area` for the
    // remaining elements
    while let Some(pos) = stack.pop() {
        let area = widths[pos] as i32 * heights[pos];
        max_area = max_area.max(area);
    }

    max_area
}
```