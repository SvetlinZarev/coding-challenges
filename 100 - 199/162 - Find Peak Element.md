# [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/)

## Problem

A peak element is an element that is strictly greater than its neighbors.

Given an integer array `nums`, find a peak element, and return its index. If the
array contains multiple peaks, return the index to any of the peaks.

You may imagine that `nums[-1] = nums[n] = -∞`

You must write an algorithm that runs in `O(log n)` time.

#### Constraints

* `1 <= nums.length <= 1000`
* `-2^31 <= nums[i] <= 2^31 - 1`
* `nums[i] != nums[i + 1]` for all valid `i`

#### Examples

```text
Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.
```

```text
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
```

## Solution

```rust
pub fn find_peak_element(nums: Vec<i32>) -> i32 {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;
        if nums[mid] > nums[mid + 1] {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    lo as i32
}
```