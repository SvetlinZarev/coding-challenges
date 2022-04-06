# [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

## Problem

### Description

Given an array of integers `nums` sorted in ascending order, find the starting
and ending position of a given target value.

If target is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

### Constraints

* `0 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`
* `nums` is a non-decreasing array.
* `-10^9 <= target <= 10^9`

### Examples

```text
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

```text
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

## Solutions

### With two binary searches

We have two versions of the binary search algorithm:

* one that returns the first occurrence
* one that returns the last occurrence

```rust
use std::convert::TryInto;

pub fn search_range(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut sol = vec![-1, -1];
    if nums.is_empty() {
        return sol;
    }

    match start(&nums, target) {
        None => return sol,
        Some(idx) => sol[0] = idx.try_into().unwrap(),
    }

    if let Some(idx) = end(&nums, target) {
        sol[1] = idx.try_into().unwrap();
    }

    sol
}

fn start(nums: &[i32], target: i32) -> Option<usize> {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;

        if nums[mid] >= target {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    if nums[hi] == target {
        return Some(hi);
    }
    None
}

fn end(nums: &[i32], target: i32) -> Option<usize> {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;

        if nums[mid] <= target {
            lo = mid + 1;
        } else if mid > 0 {
            hi = mid - 1;
        } else {
            break;
        }
    }

    if nums[hi] == target {
        return Some(hi);
    }
    None
}
```
