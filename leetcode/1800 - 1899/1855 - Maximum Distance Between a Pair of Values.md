# [1855. Maximum Distance Between a Pair of Values](https://leetcode.com/problems/maximum-distance-between-a-pair-of-values/)

## Problem

### Description

You are given two non-increasing 0-indexed integer arrays `nums1` and `nums2`.

A pair of indices `(i, j)`, where `0 <= i < nums1.length`
and `0 <= j < nums2.length`, is valid if both:

* `i <= j`
* `nums1[i] <= nums2[j]`.

Return the maximum distance of any valid pair `(i, j)`. If there are no valid
pairs, return `0`. The distance of the pair is `j - i`.

An array arr is non-increasing if `arr[i-1] >= arr[i]` for
every `1 <= i < arr.length`.

### Constraints

* `1 <= nums1.length, nums2.length <= 105`
* `1 <= nums1[i], nums2[j] <= 105`
* Both `nums1` and `nums2` are non-increasing.

### Examples

```text
Input: nums1 = [55,30,5,4,2], nums2 = [100,20,10,10,5]
Output: 2
Explanation: The valid pairs are (0,0), (2,2), (2,3), (2,4), (3,3), (3,4), and (4,4).
The maximum distance is 2 with pair (2,4).
```

```text
Input: nums1 = [2,2,2], nums2 = [10,10,1]
Output: 1
Explanation: The valid pairs are (0,0), (0,1), and (1,1).
The maximum distance is 1 with pair (0,1).
```

```text
Input: nums1 = [30,29,19,5], nums2 = [25,25,25,25,25]
Output: 2
Explanation: The valid pairs are (2,2), (2,3), (2,4), (3,3), and (3,4).
The maximum distance is 2 with pair (2,4).
```

## Solutions

The problem statement is actually asking us to find the index of the smallest
number in `nums2` that is equal or greater than `nums1[i]` for each `i`.

### Binary search (`O(n log m)`)

```rust
pub fn max_distance(nums1: &[i32], nums2: &[i32]) -> i32 {
    let mut dist = 0;

    for (i, &x) in nums1.iter().enumerate().take(nums1.len().min(nums2.len())) {
        if let Some(j) = search(&nums2[i..], x) {
            // because we have already subtracted `i` in the slice above, we 
            // just compare the `j` value, because the index is relative to `i`
            dist = dist.max(j);
        }
    }

    dist as i32
}

// The array is sorted in non-increasing order, thus the inverted lo/hi updates
fn search(array: &[i32], target: i32) -> Option<usize> {
    let mut lo = 0;
    let mut hi = array.len() - 1;

    while lo < hi {
        let mid = lo + (hi - lo + 1) / 2;

        if array[mid] < target {
            hi = mid - 1;
        } else {
            lo = mid;
        }
    }

    match array[lo] >= target {
        true => Some(lo),
        false => None,
    }
}
```

### Two pointers (`O(n)`)

```rust
pub fn max_distance(nums1: &[i32], nums2: &[i32]) -> i32 {
    let mut dist = 0;

    let mut i = 0;
    let mut j = 0;

    while i < nums1.len() && j < nums2.len() {
        // The check for `i <= j` is not necessary, because the `else` statement
        // guarantees us that `j` will always be at least as big as `i`
        if nums1[i] <= nums2[j] /* && i <= j */ {
            dist = dist.max(j - i);
            j += 1;
        } else {
            i += 1;
            j = j.max(i);
        }
    }

    dist as i32
}
```