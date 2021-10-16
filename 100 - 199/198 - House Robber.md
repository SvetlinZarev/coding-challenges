# [198. House Robber](https://leetcode.com/problems/house-robber/)

## Problem

You are a professional robber planning to rob houses along a street. Each house
has a certain amount of money stashed, the only constraint stopping you from
robbing each of them is that adjacent houses have security systems connected and
it will automatically contact the police if two adjacent houses were broken into
on the same night.

Given an integer array `nums` representing the amount of money of each house,
return the maximum amount of money you can rob tonight without alerting the
police.

#### Constraints

* `1 <= nums.length <= 100`
* `0 <= nums[i] <= 400`

#### Examples

```text
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

```text
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12.
```

## Solution

```rust
pub fn rob(nums: Vec<i32>) -> i32 {
    if nums.is_empty() {
        return 0;
    }

    let mut dp = vec![0; nums.len()];
    for idx in 0..nums.len() {
        dp[idx] = nums[idx];
        if idx >= 3 {
            dp[idx] += dp[idx - 2].max(dp[idx - 3]);
        } else if idx >= 2 {
            dp[idx] += dp[idx - 2];
        }
    }

    dp.iter().copied().rev().take(2).max().unwrap()
}
```