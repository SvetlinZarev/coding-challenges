# [136. Single Number](https://leetcode.com/problems/single-number/)

## Problem

### Description

Given a non-empty array of integers `nums`, every element appears twice except
for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only
constant extra space.

### Constraints

* Each element in the array appears twice except for one element which appears
  only once.

### Examples

```text
Input: nums = [4,1,2,1,2]
Output: 4
```

## Solutions

### XOR

```rust
pub fn single_number(nums: Vec<i32>) -> i32 {
    let mut x = 0;

    for n in nums {
        x ^= n;
    }

    x
}
```

## Related problems

* [268. Missing Number](/leetcode/200%20-%20299/268%20-%20Missing%20Number.md)
