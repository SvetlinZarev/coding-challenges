# [55. Jump Game](https://leetcode.com/problems/jump-game/)

## Problem

### Description

You are given an integer array `nums`. You are initially positioned at the
array's first index, and each element in the array represents your maximum jump
length at that position.

Return `true` if you can reach the last index, or `false` otherwise.

### Constraints

* `1 <= nums.length <= 10^4`
* `0 <= nums[i] <= 10^5`

### Examples

```text
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

```text
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump 
length is 0, which makes it impossible to reach the last index.
```

## Solutions

```rust
pub fn can_jump(nums: Vec<i32>) -> bool {
    let mut jump = 0;

    for &n in nums.iter().take(nums.len() - 1) {
        jump = jump.max(n) - 1;
        if jump < 0 {
            return false;
        }
    }

    true
}
```

```rust
pub fn can_jump(nums: Vec<i32>) -> bool {
    let mut max = 0;
    for idx in 0..nums.len() - 1 {
        let dist = nums[idx] as usize + idx;
        max = max.max(dist);

        if max == idx || max >= nums.len() - 1 {
            break;
        }
    }

    max >= nums.len() - 1
}
```

## Related Problems

* [45. Jump Game II](45%20-%20Jump%20Game%20II.md)
* [1306. Jump Game III](/leetcode/1300%20-%201399/1306%20-%20Jump%20Game%20III.md)