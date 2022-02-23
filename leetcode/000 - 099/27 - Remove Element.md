# [27. Remove Element](https://leetcode.com/problems/remove-element/)

## Problem

Given an integer array `nums` and an integer `val`, remove all occurrences
of `val` in `nums` in-place. The relative order of the elements may be changed.

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
pub fn remove_element(nums: &mut Vec<i32>, val: i32) -> i32 {
    let mut start = 0;
    let mut end = nums.len();

    while start < end {
        if nums[start] == val {
            nums.swap(start, end - 1);
            end -= 1;
            continue;
        }

        start += 1;
    }

    nums.truncate(start);
    nums.len() as i32
}
```