# [698. Partition to K Equal Sum Subsets](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/)

## Problem

Given an integer array `nums` and an integer `k`, return `true` if it is
possible to divide this array into `k` non-empty subsets whose sums are all
equal.

#### Constraints

* `1 <= k <= nums.length <= 16`
* `1 <= nums[i] <= 10^4`
* The frequency of each element is in the range `[1, 4]`.

#### Examples

```text
Input: nums = [4,3,2,3,5,2,1], k = 4
Output: true
Explanation: It's possible to divide it into 4 subsets (5), (1, 4), (2,3), (2,3) with equal sums.
```

```text
Input: nums = [1,2,3,4], k = 3
Output: false
```

## Solution

```rust
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
    nums.sort_unstable();

    // If the largest element is larger than the subset sum, then
    // it's not possible to partition the array
    if nums[nums.len() - 1] > group_sum {
        return false;
    }

    // Remove the elements of the slice, that are equal to the
    // sub-set sum. That would speed up the backtracking later
    // because we would be working with a smaller array.
    let mut nums = nums.as_slice();
    while k > 0 && nums.len() > 0 && nums[nums.len() - 1] == group_sum {
        k -= 1;
        nums = &nums[..nums.len() - 1];
    }

    // If we hit the corner case, where all elements were of the same size
    // and equal to the target sub-set sum, then we can immediately tell
    // if it's possible to partition the array
    if k == 0 && nums.len() == 0 {
        return true;
    } else if k as usize > nums.len() || k == 0 && nums.len() > 0 {
        return false;
    }

    backtrack(nums, 0, group_sum, 0)
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

fn backtrack(nums: &[i32], mask: u16, target: i32, sum: i32) -> bool {
    for (idx, n) in nums.iter().copied().rev().enumerate() {
        // Skip the already used elements
        if (1u16 << idx) & mask != 0 {
            continue;
        }

        if sum + n < target {
            if backtrack(nums, mask | (1 << idx), target, sum + n) {
                return true;
            }
        } else if sum + n == target {
            // If the current segment's sum is equal to the target sum,
            // and there are no more elements left to process, then
            // the array can be partitioned.
            // N.B.: We use `nums.len() - 1` because we have not set the
            // bitflag for the current index.
            if mask.count_ones() as usize == nums.len() - 1 {
                return true;
            }

            // There are more elements to process, each new subset starts
            // with current sum of 0
            if backtrack(nums, mask | (1 << idx), target, 0) {
                return true;
            }
        }
    }

    false
}
```