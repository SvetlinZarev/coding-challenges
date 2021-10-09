# [81. Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

## Problem

There is an integer array `nums` sorted in non-decreasing order (not necessarily
with distinct values).

Before being passed to your function, `nums` is rotated at an unknown pivot
index `k` (`0 <= k < nums.length`) such that the resulting array
is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (
0-indexed). For example, `[0,1,2,4,4,4,5,6,6,7]` might be rotated at pivot
index `5` and become `[4,5,6,6,7,0,1,2,4,4]`.

Given the array `nums` after the rotation and an integer target, return `true`
if target is in `nums`, or `false` if it is not in `nums`.

You must decrease the overall operation steps as much as possible.

**Follow up:** This problem is similar
to [Search in Rotated Sorted Array](/000%20-%20099/33%20-%20Search%20in%20Rotated%20Sorted%20Array.md)
, but `nums` may contain duplicates.

#### Constraints:

* `1 <= nums.length <= 5000`
* `-10^4 <= nums[i] <= 10^4`
* `-10^4 <= target <= 10^4`
* `nums` is guaranteed to be rotated at some pivot.

## Solution

```rust
pub fn search(nums: Vec<i32>, target: i32) -> bool {
    if nums.is_empty() {
        return false;
    }

    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;
        if target == nums[mid] {
            return true;
        }

        let mut low = lo;
        while nums[low] == nums[mid] && low < hi {
            low += 1;
        }

        let mut high = hi;
        while nums[high] == nums[mid] && high > low {
            high -= 1;
        }

        if lo != low || hi != high {
            lo = low;
            hi = high;
            continue;
        }

        if nums[lo] < nums[mid] {
            if target < nums[mid] && target >= nums[lo] {
                if mid == 0 {
                    break;
                }
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        } else {
            if target > nums[mid] && target <= nums[hi] {
                lo = mid + 1;
            } else {
                if mid == 0 {
                    break;
                }
                hi = mid - 1;
            }
        }
    }

    false
}
```
