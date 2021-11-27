# [485. Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/)

## Problem

Given a binary array `nums`, return the maximum number of consecutive `1`'s in
the array.

#### Constraints

* `1 <= nums.length <= 10^5`
* `nums[i]` is either `0` or `1`.

#### Examples

```text
Input: nums = [1,1,0,1,1,1]
Output: 3
```

> Explanation: The first two digits or the last three digits are consecutive 1s.
> The maximum number of consecutive 1s is 3.

```text
Input: nums = [1,0,1,1,0,1]
Output: 2
```

## Solution

```rust
pub fn find_max_consecutive_ones(nums: Vec<i32>) -> i32 {
    let mut best = 0;
    let mut len = 0;

    for &n in nums.iter() {
        if n == 1 {
            len += 1;
        } else {
            best = best.max(len);
            len = 0;
        }
    }

    best.max(len)
}
```