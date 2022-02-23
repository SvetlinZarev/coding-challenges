# [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/)

## Problem

Given an array of non-negative integers `nums`, you are initially positioned at
the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

You can assume that you can always reach the last index.

#### Constraints

* `1 <= nums.length <= 10^4`
* `0 <= nums[i] <= 1000`

#### Examples

```text
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

## Solution

```rust
pub fn jump(nums: Vec<i32>) -> i32 {
    let mut jumps = 0;
    let mut max_dist = 0;
    let mut position = 0;

    for idx in 0..nums.len() - 1 {
        max_dist = max_dist.max(idx + nums[idx] as usize);
        if max_dist >= nums.len() - 1 {
            jumps += 1;
            break;
        }

        if idx == position {
            position = max_dist;
            jumps += 1;
        }
    }

    jumps
}
```