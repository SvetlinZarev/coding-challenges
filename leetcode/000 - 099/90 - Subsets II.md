# [90. Subsets II](https://leetcode.com/problems/subsets-ii/)

## Problem

Given an integer array `nums` that may contain duplicates, return all possible
subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any
order.

#### Constraints

* `1 <= nums.length <= 10`
* `-10 <= nums[i] <= 10`

#### Examples

```text
Input: nums = [0]
Output: [[],[0]]
```

```text
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
```

## Solution

```rust
pub fn subsets_with_dup(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
    assert!(nums.len() <= 10);
    nums.sort_unstable();

    let mut result = HashSet::new();
    for mask in 0..((1 as usize) << nums.len()) {
        let mut bitmask = mask;
        let mut set = vec![];

        while bitmask > 0 {
            let mut idx = bitmask.trailing_zeros() as usize;
            bitmask ^= 1 << idx;

            set.push(nums[idx]);
        }

        result.insert(set);
    }

    result.drain().collect()
}
```

## Related Problems
* [78. Subsets](78%20-%20Subsets.md)
