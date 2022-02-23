# [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

## Problem

Given an integer array `nums` sorted in non-decreasing order, remove the
duplicates in-place such that each unique element appears only once. The
relative order of the elements should be kept the same.

Since it is impossible to change the length of the array in some languages, you
must instead have the result be placed in the first part of the array `nums`.
More formally, if there are `k` elements after removing the duplicates, then the
first `k` elements of `nums` should hold the final result. It does not matter
what you leave beyond the first `k` elements.

Return `k` after placing the final result in the first `k` slots of `nums`.

Do not allocate extra space for another array. You must do this by modifying the
input array in-place with O(1) extra memory.

## Solution

```rust
pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
    let mut idx = 0;
    let mut pos = 1;

    while pos < nums.len() {
        if nums[idx] != nums[pos] {
            idx += 1;
            nums[idx] = nums[pos];
        }

        pos += 1;
    }

    nums.truncate(idx + 1);
    nums.len() as i32
}
```