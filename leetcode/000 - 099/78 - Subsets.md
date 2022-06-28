# [78. Subsets](https://leetcode.com/problems/subsets/)

## Problem

### Description

Given an integer array `nums` of unique elements, return all possible subsets (
the power set).

The solution set must not contain duplicate subsets. Return the solution in any
order.

### Constraints

* All the numbers of `nums` are unique.
* `1 <= nums.length <= 10`
* `-10 <= nums[i] <= 10`

### Examples

```text
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

```text
Input: nums = [0]
Output: [[],[0]]
```

## Solutions

### Using bitmasks

```rust
pub fn subsets(nums: Vec<i32>) -> Vec<Vec<i32>> {
    let mut limit = 0u32;
    for shift in 0..nums.len() {
        limit |= 1 << shift;
    }

    let mut result = vec![];
    for mut mask in 0..=limit {
        let mut set = vec![];
        while mask > 0 {
            let pos = mask.trailing_zeros() as usize;
            set.push(nums[pos]);
            mask = mask & (mask - 1);
        }
        result.push(set);
    }

    result
}
```

## Related Problems

* [90. Subsets II](90%20-%20Subsets%20II.md)