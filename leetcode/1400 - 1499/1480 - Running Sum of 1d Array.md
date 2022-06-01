# [1480. Running Sum of 1d Array](https://leetcode.com/problems/running-sum-of-1d-array/)

## Problem

### Description

Given an array `nums`. We define a running sum of an array
as `runningSum[i] = sum(nums[0]…nums[i])`.

Return the running sum of `nums`.

### Constraints

* `1 <= nums.length <= 1000`
* `-10^6 <= nums[i] <= 10^6`

### Examples

```text
Input: nums = [1,2,3,4]
Output: [1,3,6,10]
Explanation: Running sum is obtained as follows: [1, 1+2, 1+2+3, 1+2+3+4].
```

```text
Input: nums = [1,1,1,1,1]
Output: [1,2,3,4,5]
Explanation: Running sum is obtained as follows: [1, 1+1, 1+1+1, 1+1+1+1, 1+1+1+1+1].
```

```text
nput: nums = [3,1,2,10,1]
Output: [3,4,6,16,17]
```

## Solutions

### In-place calculation

```rust
pub fn running_sum(mut nums: Vec<i32>) -> Vec<i32> {
    for idx in 1..nums.len() {
        nums[idx] += nums[idx - 1];
    }
    nums
}
```
