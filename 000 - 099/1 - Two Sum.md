# [1. Two Sum](https://leetcode.com/problems/two-sum/)

## Problem

Given an array of integers `nums` and an integer `target`, return indices of the
two numbers such that they add up to `target`.

You may assume that each input would have exactly one solution, and you may not
use the same element twice.

You can return the answer in any order.

## Solutions

### Two pass O(n) solution using a HashMap

```rust
    pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let diffs = nums
        .iter()
        .enumerate()
        .map(|(idx, value)| (target - value, idx))
        .collect::<HashMap<i32, usize>>();

    for (idx, val) in nums.iter().copied().enumerate() {
        if let Some(&i) = diffs.get(&val) {
            if i != idx {
                return vec![idx as i32, i as i32];
            }
        }
    }

    unreachable!("there is an answer according to the problem constraints")
}
```

### Single pass O(n) solution using a HashMap

```rust
use std::collections::HashMap;

pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut indexes = HashMap::with_capacity(nums.len() + 8);

    for (idx, val) in nums.iter().copied().enumerate() {
        if let Some(&index) = indexes.get(&(target - val)) {
            if index != idx {
                return vec![index as i32, idx as i32];
            }
        }

        indexes.insert(val, idx);
    }

    unreachable!("there is an answer according to the problem constraints")
}
```
