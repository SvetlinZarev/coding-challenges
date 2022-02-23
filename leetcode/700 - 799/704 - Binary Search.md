# [704. Binary Search](https://leetcode.com/problems/binary-search/)

# Problem

Given an array of integers `nums` which is sorted in ascending order, and an
integer `target`, write a function to search `target` in `nums`. If `target`
exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

#### Constraints:

* `1 <= nums.length <= 10^4`
* `-10^4 < nums[i], target < 10^4`
* All the integers in `nums` are unique.
* `nums` is sorted in ascending order.

## Solution

```rust
    pub fn search(nums: Vec<i32>, target: i32) -> i32 {
    if nums.is_empty() {
        return -1;
    }

    let mut hi = nums.len() - 1;
    let mut lo = 0;

    while hi >= lo {
        let mid = (hi - lo) / 2 + lo;
        if nums[mid] == target {
            return mid as i32;
        }

        if target > nums[mid] {
            lo = mid + 1;
        } else {
            if mid == 0 {
                return -1;
            }

            hi = mid - 1;
        }
    }

    -1
}
```
