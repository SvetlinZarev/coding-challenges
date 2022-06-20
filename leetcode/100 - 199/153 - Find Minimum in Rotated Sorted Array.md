# [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

## Problem

### Description

Suppose an array of length `n` sorted in ascending order is rotated between `1`
and `n` times. For example, the array `nums = [0,1,2,4,5,6,7]` might become:

* `[4,5,6,7,0,1,2]` if it was rotated 4 times.
* `[0,1,2,4,5,6,7]` if it was rotated 7 times.

Notice that rotating an array `[a[0], a[1], a[2], ..., a[n-1]]` 1 time results
in the array `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]`.

Given the sorted rotated array `nums` of unique elements, return the minimum
element of this array.

You must write an algorithm that runs in `O(log n)` time.

### Constraints

* `n == nums.length`
* `1 <= n <= 5000`
* `-5000 <= nums[i] <= 5000`
* All the integers of `nums` are unique.
* `nums` is sorted and rotated between `1` and `n` times.

### Examples

```text
Input: nums = [4,5,6,7,0,1,2]
Output: 0
Explanation: The original array was [0,1,2,4,5,6,7] and it was rotated 4 times.
```

## Solutions

### Binary search

```rust
pub fn find_min(nums: Vec<i32>) -> i32 {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo < hi {
        let mid = (hi + lo) / 2;
        if nums[mid] > nums[hi] {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }

    nums[lo]
}
```
