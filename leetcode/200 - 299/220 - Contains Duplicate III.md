# [220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)

## Problem

### Description

Given an integer array `nums` and two integers `k` and `t`, return `true` if
there are two distinct indices `i` and `j` in the array such
that `abs(nums[i] - nums[j]) <= t` and `abs(i - j) <= k`.

### Constraints

* `1 <= nums.length <= 2 * 10^4`
* `-2^31 <= nums[i] <= 2^31 - 1`
* `0 <= k <= 1^04`
* `0 <= t <= 2^31 - 1`

### Examples

```text
Input: nums = [1,2,3,1], k = 3, t = 0
Output: true
```

```text
Input: nums = [1,0,1,1], k = 1, t = 2
Output: true
```

```text
Input: nums = [1,5,9,1,5,9], k = 2, t = 3
Output: false
```

## Solutions

### Sliding window + TreeSet

The question is to find a number that is the range `nums[idx] +- t` that is
within a sliding window of `k+1` elements.

Because the problems constraints allow `nums[idx]` to be in the
range `-2^31 <= nums[i] <= 2^31 - 1` we cast all the number to `i64` in order to
avoid overflow issues

```rust
use std::collections::BTreeSet;

pub fn contains_nearby_almost_duplicate(nums: Vec<i32>, k: i32, t: i32) -> bool {
    assert!(k >= 0);
    assert!(t >= 0);

    let t = t as i64;
    let k = k as usize;
    if k == 0 {
        return false;
    }

    let mut set = BTreeSet::new();
    for idx in 0..nums.len() {
        let n = nums[idx] as i64;

        let mut range = set.range(n - t..=n + t);
        if let Some(_) = range.next() {
            return true;
        }

        set.insert(nums[idx] as i64);
        if set.len() > k {
            set.remove(&(nums[idx - k] as i64));
        }
    }

    false
}
```

Alternatively to casting to `i64` we can use the `saturating_sub()`
& `saturating_add()` methods:

```rust
use std::collections::BTreeSet;

pub fn contains_nearby_almost_duplicate(nums: Vec<i32>, k: i32, t: i32) -> bool {
    assert!(k >= 0);
    assert!(t >= 0);

    let k = k as usize;
    if k == 0 {
        return false;
    }

    let mut set = BTreeSet::new();
    for idx in 0..nums.len() {
        let n = nums[idx];
        let from = n.saturating_sub(t);
        let to = n.saturating_add(t);

        let mut range = set.range(from..=to);
        if let Some(_) = range.next() {
            return true;
        }

        set.insert(nums[idx]);
        if set.len() > k {
            set.remove(&nums[idx - k]);
        }
    }

    false
}
```

## Related Problems

* [217. Contains Duplicate](217%20-%20Contains%20Duplicate.md)
* [219. Contains Duplicate II](219%20-%20Contains%20Duplicate%20II.md)
