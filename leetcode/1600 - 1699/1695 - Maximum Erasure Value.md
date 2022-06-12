# [1695. Maximum Erasure Value](https://leetcode.com/problems/maximum-erasure-value/)

## Problem

### Description

You are given an array of positive integers `nums` and want to erase a subarray
containing unique elements. The score you get by erasing the subarray is equal
to the sum of its elements.

Return the maximum score you can get by erasing exactly one subarray.

An array `b` is called to be a subarray of `a` if it forms a contiguous
subsequence of `a`, that is, if it is equal to `a[l],a[l+1],...,a[r]` for
some `(l,r)`.

### Constraints

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^4`

### Examples

```text
Input: nums = [4,2,4,5,6]
Output: 17
Explanation: The optimal subarray here is [2,4,5,6].
```

```Input: nums = [5,2,1,2,5,2,1,2,5]
Output: 8
Explanation: The optimal subarray here is [5,2,1] or [1,2,5].
```

## Solutions

### Sliding window with prefix sum

```rust
use std::collections::hash_map::Entry;
use std::collections::HashMap;

pub fn maximum_unique_subarray(nums: &[i32]) -> i32 {
    let mut l = 0;
    let mut r = 0;

    let mut sum = 0;
    let mut answer = 0;

    let mut last_seen = HashMap::new();
    while r < nums.len() {
        sum += nums[r];

        match last_seen.entry(nums[r]) {
            Entry::Vacant(e) => {
                e.insert(r);
            }

            Entry::Occupied(mut e) => {
                // The index where we last saw that number
                let last_idx = *e.get();

                // If the last occurrence is within the current window,
                // we have to subtract all values up to that position
                // (including the repeating number) and shrink the
                // sliding window
                while l <= last_idx {
                    sum -= nums[l];
                    l += 1;
                }

                // update the last-seen position with the current one
                e.insert(r);
            }
        }

        r += 1;
        answer = answer.max(sum);
    }

    answer
}
```
