# [413. Arithmetic Slices](https://leetcode.com/problems/arithmetic-slices/)

## Problem

An integer array is called *arithmetic* if it consists of at least three
elements and if the difference between any two consecutive elements is the same.
For example, `[1,3,5,7,9]`, `[7,7,7,7]`, and `[3,-1,-5,-9]` are arithmetic
sequences.

Given an integer array `nums`, return the number of arithmetic subarrays
of `nums`.

A subarray is a contiguous subsequence of the array.

#### Constraints

* `1 <= nums.length <= 5000`
* `-1000 <= nums[i] <= 1000`

#### Examples

```text
Input: nums = [1,2,3,4]
Output: 3
Explanation: We have 3 arithmetic slices in nums: [1, 2, 3], [2, 3, 4] and [1,2,3,4] itself.
```

```text
Input: nums = [1]
Output: 0
```

## Solution

```rust
pub fn number_of_arithmetic_slices(nums: Vec<i32>) -> i32 {
    if nums.len() < 3 {
        return 0;
    }

    let mut prev = 0;
    let mut diff = nums[1] - nums[0];
    let mut answer = 0;

    for idx in 2..nums.len() {
        if diff == nums[idx] - nums[idx - 1] {
            prev = prev + 1;
        } else {
            diff = nums[idx] - nums[idx - 1];
            prev = 0;
        }

        answer += prev;
    }

    answer
}
```