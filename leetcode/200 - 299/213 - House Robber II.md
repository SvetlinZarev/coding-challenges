# [213. House Robber II](https://leetcode.com/problems/house-robber-ii/)

## Problem

You are a professional robber planning to rob houses along a street. Each house
has a certain amount of money stashed. All houses at this place are arranged in
a circle. That means the first house is the neighbor of the last one. Meanwhile,
adjacent houses have a security system connected, and it will automatically
contact the police if two adjacent houses were broken into on the same night.

Given an integer array `nums` representing the amount of money of each house,
return the maximum amount of money you can rob tonight without alerting the
police.

#### Constraints

* `1 <= nums.length <= 100`
* `0 <= nums[i] <= 400`

#### Examples

```text
Input: nums = [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2), because they are adjacent houses.
```

```text
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

```text
Input: nums = [1,2,3]
Output: 3
```

#### Hint

Since `House[1]` and `House[n]` are adjacent, they cannot be robbed together.
Therefore, the problem becomes to rob either `House[1] to House[n-1]`
or `House[2] to House[n]`, depending on which choice offers more money. Now the
problem has degenerated to the [House Robber], which is already been solved.

## Solution

```rust
pub fn rob(nums: Vec<i32>) -> i32 {
    if nums.len() == 0 {
        return 0;
    }

    if nums.len() == 1 {
        return nums[0];
    }

    if nums.len() == 2 {
        return nums[0].max(nums[1]);
    }

    let a = rob_slice(&nums[..nums.len() - 1]);
    let b = rob_slice(&nums[1..nums.len()]);

    a.max(b)
}

fn rob_slice(nums: &[i32]) -> i32 {
    if nums.len() == 0 {
        return 0;
    }

    if nums.len() == 1 {
        return nums[0];
    }

    if nums.len() == 2 {
        return nums[0].max(nums[1]);
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

## Related Questions

* [House Robber]

[House Robber]: /leetcode/100%20-%20199/198%20-%20House%20Robber.md
