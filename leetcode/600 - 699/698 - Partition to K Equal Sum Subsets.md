# [698. Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)

## Problem

### Description

Given an integer array `nums` and an integer `k`, return `true` if it is
possible to divide this array into `k` non-empty subsets whose sums are all
equal.

### Constraints

* `1 <= k <= nums.length <= 16`
* `1 <= nums[i] <= 10^4`
* The frequency of each element is in the range `[1, 4]`.

### Examples

```text
Input: nums = [4,3,2,3,5,2,1], k = 4
Output: true
Explanation: It's possible to divide it into 4 subsets (5), (1, 4), (2,3), (2,3) with equal sums.
```

```text
Input: nums = [1,2,3,4], k = 3
Output: false
```

## Solutions

### Backtracking with bitmasks and memoization

```rust
use std::collections::HashSet;

pub fn can_partition_k_subsets(mut nums: Vec<i32>, mut k: i32) -> bool {
    assert!(nums.len() <= 16);
    let group_sum = match find_group_sum(&nums, k) {
        None => return false,
        Some(sum) => sum,
    };

    // As we have only up to 16 elements it's cheap to sort the array
    // That would allow us to easily check if it's not possible to
    // partition the array and also allow us to reduce the size of
    // the array, thus speeding up the backtracking later
    nums.sort_unstable_by(|a, b| a.cmp(b).reverse());

    // If the largest element is larger than the subset sum, then
    // it's not possible to partition the array
    if nums[0] > group_sum {
        return false;
    }

    // Remove the elements of the slice, that are equal to the
    // sub-set sum. That would speed up the backtracking later
    // because we would be working with a smaller array.
    let mut nums = nums.as_slice();
    while k > 0 && nums.len() > 0 && nums[0] == group_sum {
        k -= 1;
        nums = &nums[1..];
    }

    // If we hit the corner case, where all elements were of the same size
    // and equal to the target sub-set sum, then we can immediately tell
    // if it's possible to partition the array
    if k == 0 && nums.len() == 0 {
        return true;
    }

    if k as usize > nums.len() || k == 0 && nums.len() > 0 {
        return false;
    }

    let mut failures = HashSet::new();
    backtrack(nums, &mut failures, 0, group_sum, 0)
}

fn find_group_sum(nums: &[i32], groups: i32) -> Option<i32> {
    if nums.is_empty() || groups as usize > nums.len() {
        return None;
    }

    let sum = nums.iter().sum::<i32>();
    if sum % groups != 0 {
        return None;
    }

    Some(sum / groups)
}

fn backtrack(
    nums: &[i32],
    failures: &mut HashSet<u16>,
    mask: u16,
    target: i32,
    mut sum: i32,
) -> bool {
    if failures.contains(&mask) {
        return false;
    }

    if target == sum {
        if mask.count_ones() == nums.len() as u32 {
            return true;
        }

        sum = 0;
    }

    for idx in 0..nums.len() {
        // Skip the already used elements
        if (1u16 << idx) & mask != 0 {
            continue;
        }

        let next_sum = sum + nums[idx];

        if next_sum <= target {
            let next_mask = mask | (1 << idx);

            if backtrack(nums, failures, next_mask, target, next_sum) {
                return true;
            }
        }
    }

    failures.insert(mask);
    false
}
```

### DFS

```rust
pub fn can_partition_k_subsets(mut nums: Vec<i32>, k: i32) -> bool {
    assert!(k > 0);
    assert!(k <= 16);
    assert!(k as usize <= nums.len());

    let sum = nums.iter().sum::<i32>();

    // If the sum of all numbers is not divisible by `k`, then
    // it is not possible to partition the array to `k` subsets
    if sum % k != 0 {
        return false;
    }

    // The expected sum of the elements of each subset
    let target = sum / k;

    // We need to sort the nums for two reasons:
    // * if it is sorted in descending order, negative states from the DFS
    //   will be reached faster, thus speeding up the algorithm
    // * we can also check if there are numbers larger than the subset sum
    //   Although this can be done in O(n) time, we can take advantage of
    //   the sorting we did for the previous point
    nums.sort_unstable_by(|a, b| a.cmp(b).reverse());

    // If there is a number that is larger than the subset sum,
    // then it is not possible to partition the array to `k` subsets
    if nums[0] > target {
        return false;
    }

    // If there are numbers that are equal to the target subset sum,
    // we can remove them early in order to have fewer elements for
    // the DFS
    let mut nums = &mut nums[..];
    let mut k = k as usize;
    while k > 0 && nums[0] == target {
        nums = &mut nums[1..];
        k -= 1;
    }

    // Fast path: all elements were equal to the target subset sum
    if k == 0 {
        return true;
    }

    let mut sums = vec![0; k as usize];
    dfs(&nums, &mut sums, target, 0)
}

fn dfs(nums: &[i32], sums: &mut [i32], target: i32, idx: usize) -> bool {
    if nums.len() == idx {
        return sums.iter().all(|&x| x == target);
    }

    for s in 0..sums.len() {
        if sums[s] + nums[idx] > target {
            continue;
        }

        // If the two subsets have an equal current value, then
        // we need to check if the next value can put in only one
        // of them. There is no need to check every subset, given that
        // they are equal.
        //
        // Note: Is the upper statement always true ? 
        if s > 0 && sums[s - 1] == sums[s] {
            continue;
        }

        sums[s] += nums[idx];
        if dfs(nums, sums, target, idx + 1) {
            return true;
        }
        sums[s] -= nums[idx];
    }

    false
}
```

This implementation works for all already seen & equal subset values, not just
for consecutive ones.

```rust
pub fn can_partition_k_subsets(mut nums: Vec<i32>, k: i32) -> bool {
    assert!(k > 0);
    assert!(k <= 16);
    assert!(k as usize <= nums.len());

    let sum = nums.iter().sum::<i32>();

    // If the sum of all numbers is not divisible by `k`, then
    // it is not possible to partition the array to `k` subsets
    if sum % k != 0 {
        return false;
    }

    // The expected sum of the elements of each subset
    let target = sum / k;

    // We need to sort the nums for two reasons:
    // * if it is sorted in descending order, negative states from the DFS
    //   will be reached faster, thus speeding up the algorithm
    // * we can also check if there are numbers larger than the subset sum
    //   Although this can be done in O(n) time, we can take advantage of
    //   the sorting we did for the previous point
    nums.sort_unstable_by(|a, b| a.cmp(b).reverse());

    // If there is a number that is larger than the subset sum,
    // then it is not possible to partition the array to `k` subsets
    if nums[0] > target {
        return false;
    }

    // If there are numbers that are equal to the target subset sum,
    // we can remove them early in order to have fewer elements for
    // the DFS
    let mut nums = &mut nums[..];
    let mut k = k as usize;
    while k > 0 && nums[0] == target {
        nums = &mut nums[1..];
        k -= 1;
    }

    // Fast path: all elements were equal to the target subset sum
    if k == 0 {
        return true;
    }

    let mut sums = vec![0; k];
    dfs(&nums, &mut sums, target, 0)
}

fn dfs(nums: &[i32], sums: &mut [i32], target: i32, idx: usize) -> bool {
    if nums.len() == idx {
        return sums.iter().all(|&x| x == target);
    }

    for s in 0..sums.len() {
        if sums[s] + nums[idx] > target {
            continue;
        }

        // If the two subsets have an equal current value, then
        // we need to check if the next value can put in only one
        // of them. There is no need to check every subset, given that
        // they are equal.
        //
        // Note: Is the upper statement always true ?
        if sums
            .iter()
            .copied()
            .enumerate()
            .filter(|&(pos, _)| pos < s)
            .any(|(_, val)| val == sums[s])
        {
            continue;
        }

        sums[s] += nums[idx];
        if dfs(nums, sums, target, idx + 1) {
            return true;
        }
        sums[s] -= nums[idx];
    }

    false
}
```

## Related Problems

* [473. Matchsticks to Square](/leetcode/400%20-%20499/473%20-%20Matchsticks%20to%20Square.md)
