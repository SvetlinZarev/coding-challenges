# [912. Sort an Array](https://leetcode.com/problems/sort-an-array/)

## Problem

Given an array of integers `nums`, sort the array in ascending order.

#### Constraints

* `1 <= nums.length <= 5 * 10^4`
* `-5 * 10^4 <= nums[i] <= 5 * 10^4`

#### Examples

```text
Input: nums = [5,2,3,1]
Output: [1,2,3,5]
```

## Solutions

### Cheating

```rust
pub fn sort_array(mut nums: Vec<i32>) -> Vec<i32> {
    nums.sort_unstable();
    nums
}
```