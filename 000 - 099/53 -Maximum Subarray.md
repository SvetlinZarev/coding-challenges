# [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

## Problem

Given an integer array `nums`, find the contiguous subarray (containing at least
one number) which has the largest sum and return its sum.

A subarray is a contiguous part of an array.

#### Examples

```text
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

```text
Input: nums = [5,4,-1,7,8]
Output: 23
```

## Solution

```rust
pub fn max_sub_array(nums: Vec<i32>) -> i32 {
    if nums.is_empty() {
        return 0;
    }

    let mut best = nums[0];
    let mut sum = nums[0];

    nums.iter().skip(1).for_each(|&n| {
        if sum <= 0 {
            sum = n;
        } else {
            sum += n;
        }

        best = best.max(sum);
    });

    best
}
```