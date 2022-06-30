# [462. Minimum Moves to Equal Array Elements II](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/)

## Problem

### Description

Given an integer array `nums` of size `n`, return the minimum number of moves
required to make all array elements equal.

In one move, you can increment or decrement an element of the array by `1`.

Test cases are designed so that the answer will fit in a 32-bit integer.

### Constraints

* `n == nums.length`
* `1 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`

### Examples

```text
Input: nums = [1,2,3]
Output: 2
Explanation:
Only two moves are needed (remember each move increments or decrements one element):
[1,2,3]  =>  [2,2,3]  =>  [2,2,2]
```

```text
Input: nums = [1,10,2,9]
Output: 16
```

## Solutions

### Calculating the difference between the elements and the median

```rust
pub fn min_moves2(mut nums: Vec<i32>) -> i32 {
    if nums.is_empty() {
        return 0;
    }

    nums.sort_unstable();

    let mid = nums.len() / 2;
    let median = if nums.len() % 2 == 0 {
        (nums[mid - 1] + nums[mid]) / 2
    } else {
        nums[mid]
    };

    nums.iter().fold(0, |acc, &val| acc + (val - median).abs())
}
```
