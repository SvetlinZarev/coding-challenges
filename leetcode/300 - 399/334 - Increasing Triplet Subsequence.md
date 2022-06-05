# [334. Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/)

## Problem

### Description

Given an integer array `nums`, return `true` if there exists a triple of
indices `(i, j, k)` such that `i < j < k` and `nums[i] < nums[j] < nums[k]`. If
no such indices exists, return `false`.

### Constraints

* `1 <= nums.length <= 5 * 10^5`
* `-2^31 <= nums[i] <= 2^31 - 1`

### Examples

```text
Input: nums = [1,2,3,4,5]
Output: true
Explanation: Any triplet where i < j < k is valid.
```

```text
Input: nums = [5,4,3,2,1]
Output: false
Explanation: No triplet exists.
```

```text
Input: nums = [2,1,5,0,4,6]
Output: true
Explanation: The triplet (3, 4, 5) is valid because nums[3] == 0 < nums[4] == 4 < nums[5] == 6.
```

## Solutions

```rust
pub fn increasing_triplet(nums: Vec<i32>) -> bool {
    let mut min = i32::MAX;
    let mut mid = i32::MAX;

    for n in nums {
        if n <= min {
            min = n;
        } else if n <= mid {
            mid = n;
        } else {
            return true;
        }
    }

    false
}
```
