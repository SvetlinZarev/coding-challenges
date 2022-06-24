# [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)

## Problem

### Description

Given an array of positive integers `nums` and a positive integer `target`,
return the minimal length of a contiguous
subarray `[numsl, numsl+1, ..., numsr-1, numsr]` of which the sum is greater
than or equal to `target`. If there is no such subarray, return `0` instead.

### Constraints

* `1 <= target <= 10^9`
* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^5`

### Examples

```text
Input: target = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: The subarray [4,3] has the minimal length under the problem constraint.
```

```text
Input: target = 4, nums = [1,4,4]
Output: 1
```

```text
Input: target = 11, nums = [1,1,1,1,1,1,1,1]
Output: 0
```

## Solutions

### Sliding Window

```rust
pub fn min_sub_array_len(target: i32, nums: Vec<i32>) -> i32 {
    assert!(target > 0);

    let mut answer = usize::MAX;
    let mut sum = 0;
    let mut start = 0;

    for end in 0..nums.len() {
        sum += nums[end];

        while sum >= target {
            answer = answer.min(end - start + 1);
            sum -= nums[start];
            start += 1;
        }
    }

    if answer == usize::MAX {
        answer = 0;
    }

    answer as i32
}
```