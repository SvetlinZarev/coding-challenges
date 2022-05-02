# [905. Sort Array By Parity](https://leetcode.com/problems/sort-array-by-parity/)

## Problem

### Description

Given an integer array `nums`, move all the even integers at the beginning of
the array followed by all the odd integers.

Return any array that satisfies this condition.

### Constraints

* `1 <= nums.length <= 5000`
* `0 <= nums[i] <= 5000`

### Examples

```text
Input: nums = [3,1,2,4]
Output: [2,4,3,1]
Explanation: The outputs [4,2,3,1], [2,4,1,3], and [4,2,1,3] would also be accepted.
```

```text
Input: nums = [0]
Output: [0]
```

## Solutions

### Two pointers

```rust
pub fn sort_array_by_parity(mut nums: Vec<i32>) -> Vec<i32> {
    let mut l = 0;
    let mut r = nums.len() - 1;

    while l < r {
        while l < r && nums[l] % 2 == 0 {
            l += 1;
        }

        while l < r && nums[r] % 2 != 0 {
            r -= 1;
        }

        if l < r {
            nums.swap(l, r);
            l += 1;
            r -= 1;
        }
    }

    nums
}
```
