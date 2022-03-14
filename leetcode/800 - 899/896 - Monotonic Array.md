# [896. Monotonic Array](https://leetcode.com/problems/monotonic-array/)

## Problem

### Description

An array is monotonic if it is either monotone increasing or monotone
decreasing.

An array `nums` is monotone increasing if for all `i <= j, nums[i] <= nums[j]`.
An array `nums` is monotone decreasing if for all `i <= j, nums[i] >= nums[j]`.

Given an integer array `nums`, return `true` if the given array is monotonic,
or `false` otherwise.

### Constraints

* `1 <= nums.length <= 10^5`
* `-10^5 <= nums[i] <= 10^5`

### Examples

```text
Input: nums = [1,2,2,3]
Output: true
```

```text
Input: nums = [6,5,4,4]
Output: true
```

```text
Input: nums = [1,3,2]
Output: false
```

## Solutions

```rust
use std::cmp::Ordering;

pub fn is_monotonic(nums: Vec<i32>) -> bool {
    if nums.len() <= 2 {
        return true;
    }

    let mut ordering = nums[0].cmp(&nums[nums.len() - 1]);

    nums.windows(2).all(|w| {
        let order = w[0].cmp(&w[1]);
        order == Ordering::Equal || order == ordering
    })
}
```