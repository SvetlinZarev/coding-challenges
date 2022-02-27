# [976. Largest Perimeter Triangle](https://leetcode.com/problems/largest-perimeter-triangle/)

## Problem

### Description

Given an integer array `nums`, return the largest perimeter of a triangle with a
non-zero area, formed from three of these lengths. If it is impossible to form
any triangle of a non-zero area, return `0`.

### Constraints

* `3 <= nums.length <= 10^4`
* `1 <= nums[i] <= 10^6`

### Examples

```text
Input: nums = [2,1,2]
Output: 5
```

```text
Input: nums = [1,2,1]
Output: 0
```

## Solutions

### `O(n log n)` - With Sorting

```rust
use std::cmp::Reverse;

pub fn largest_perimeter(mut nums: Vec<i32>) -> i32 {
    nums.sort_unstable_by_key(|x| Reverse(*x));

    for idx in 2..nums.len() {
        // triangle inequality
        if nums[idx] + nums[idx - 1] > nums[idx - 2] {
            return nums[idx] + nums[idx - 1] + nums[idx - 2];
        }
    }

    0
}
```

## Resources

* [Triangle inequality](https://en.wikipedia.org/wiki/Triangle_inequality)

## Related Problems

* [812. Largest Triangle Area](/leetcode/800%20-%20899/812%20-%20Largest%20Triangle%20Area.md)