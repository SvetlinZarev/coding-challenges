# [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

## Problem

Given a sorted array of distinct integers and a target value, return the index
if the target is found. If not, return the index where it would be if it were
inserted in order.

You must write an algorithm with `O(log n)` runtime complexity.

#### Examples

```text
Input: nums = [1,3,5,6], target = 5
Output: 2
```

```text
Input: nums = [1,3,5,6], target = 2
Output: 1
```

## Solution

```rust
pub fn search_insert(nums: Vec<i32>, target: i32) -> i32 {
    if nums.is_empty() {
        return 0;
    }

    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while hi >= lo {
        let mid = (hi - lo) / 2 + lo;
        if nums[mid] == target {
            return mid as i32;
        }

        if nums[mid] > target {
            if mid == 0 {
                return 0;
            }
            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }

    lo as i32
}
```