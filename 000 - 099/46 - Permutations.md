# [46. Permutations](https://leetcode.com/problems/permutations/)

## Problem

Given an array `nums` of distinct integers, return all the possible
permutations. You can return the answer in any order.

#### Constraints

* `1 <= nums.length <= 6`
* `-10 <= nums[i] <= 10`
* All the integers of `nums` are unique.

#### Examples

```text
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

```text
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

## Solution

```rust
pub fn permute(nums: Vec<i32>) -> Vec<Vec<i32>> {
    let mut permutations = vec![];
    recurse(&mut permutations, vec![], &nums);
    permutations
}


fn recurse(solution: &mut Vec<Vec<i32>>, permutation: Vec<i32>, remaining: &[i32]) {
    if remaining.is_empty() {
        solution.push(permutation);
        return;
    }

    for (idx, &v) in remaining.iter().enumerate() {
        let remaining = remaining
            .iter()
            .copied()
            .enumerate()
            .filter(|&(i, _)| i != idx)
            .map(|(_, v)| v)
            .collect::<Vec<_>>();

        let mut permutation = permutation.clone();
        permutation.push(v);

        recurse(solution, permutation, &remaining);
    }
}
```