# [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

## Problem

There is an integer array `nums` sorted in ascending order (with **distinct**
values).

Prior to being passed to your function, `nums` is possibly rotated at an unknown
pivot index `k` (`1 <= k < nums.length`) such that the resulting array
is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (
0-indexed). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `3`
and become `[4,5,6,7,0,1,2]`.

Given the array `nums` after the possible rotation and an integer target, return
the index of target if it is in `nums`, or `-1` if it is not in `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

#### Constraints:

* `1 <= nums.length <= 5000`
* `-10^4 <= nums[i] <= 10^4`
* `-10^4 <= target <= 10^4`
* `nums` is an ascending array that is possibly rotated.
* all values of `nums` are unique.

## Solution

```rust
    pub fn search(nums: Vec<i32>, target: i32) -> i32 {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo <= hi {
        let mid = (lo + hi) / 2;
        if target == nums[mid] {
            return mid as i32;
        }

        if nums[lo] <= nums[mid] {
            if target >= nums[lo] && target <= nums[mid] {
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        } else {
            if target <= nums[hi] && target >= nums[mid] {
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
    }

    -1
}
```
