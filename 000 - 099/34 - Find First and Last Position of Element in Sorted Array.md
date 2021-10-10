# [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

## Problem

Given an array of integers `nums` sorted in ascending order, find the starting
and ending position of a given target value.

If target is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

#### Examples

```text
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

```text
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

## Solution

```rust
use std::cmp::Ordering;

pub fn search_range(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut sol = vec![-1, -1];
    if nums.is_empty() {
        return sol;
    }

    match start(&nums, target) {
        None => return sol,
        Some(idx) => sol[0] = idx,
    }

    if let Some(idx) = end(&nums, target) {
        sol[1] = idx;
    }

    sol
}

fn start(nums: &[i32], target: i32) -> Option<i32> {
    let mut lo = 0;
    let mut hi = nums.len() - 1;
    let mut res = None;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        match target.cmp(&nums[mid]) {
            Ordering::Less => {
                if mid == 0 {
                    break;
                }
                hi = mid - 1
            }
            Ordering::Equal => {
                res = Some(mid as i32);
                if mid == 0 {
                    break;
                }
                hi = mid - 1;
            }
            Ordering::Greater => {
                lo = mid + 1;
            }
        }
    }

    res
}

fn end(nums: &[i32], target: i32) -> Option<i32> {
    let mut lo = 0;
    let mut hi = nums.len() - 1;
    let mut res = None;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        match target.cmp(&nums[mid]) {
            Ordering::Less => {
                if mid == 0 {
                    break;
                }
                hi = mid - 1
            }
            Ordering::Equal => {
                res = Some(mid as i32);
                lo = mid + 1;
            }
            Ordering::Greater => {
                lo = mid + 1;
            }
        }
    }

    res
}
```