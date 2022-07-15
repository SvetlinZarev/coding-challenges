# [219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)

## Problem

### Description

Given an integer array `nums` and an integer `k`, return `true` if there are two
distinct indices `i` and `j` in the array such that `nums[i] == nums[j]`
and `abs(i - j) <= k`.

### Constraints

* `1 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`
* `0 <= k <= 10^5`

### Examples

```text
Input: nums = [1,2,3,1], k = 3
Output: true
```

```text
Input: nums = [1,0,1,1], k = 1
Output: true
```

```text
Input: nums = [1,2,3,1,2,3], k = 2
Output: false
```

## Solutions

### Sliding Window + HashSet

Maintain a window of `k + 1` elements inside the set.

```rust
use std::collections::HashSet;

pub fn contains_nearby_duplicate(nums: Vec<i32>, k: i32) -> bool {
    assert!(k >= 0);

    let k = k as usize;
    if k == 0 {
        return false;
    }

    let mut set = HashSet::new();
    for idx in 0..nums.len() {
        if !set.insert(nums[idx]) {
            return true;
        }

        if set.len() > k {
            set.remove(&nums[idx - k]);
        }
    }

    false
}
```

## Related Problems

* [217. Contains Duplicate](217%20-%20Contains%20Duplicate.md)
* [220. Contains Duplicate III](220%20-%20Contains%20Duplicate%20III.md)
