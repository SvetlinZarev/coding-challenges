# [47. Permutations II](https://leetcode.com/problems/permutations-ii/)

## Problem

Given a collection of numbers, `nums`, that might contain duplicates, return all
possible unique permutations in any order

#### Constraints

* `1 <= nums.length <= 8`
* `-10 <= nums[i] <= 10`

#### Examples

```text
Input: nums = [1,1,2]
Output:
[
 [1,1,2],
 [1,2,1],
 [2,1,1]
]
```

```text
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

## Solution

```rust
use std::collections::HashSet;

pub fn permute_unique(nums: Vec<i32>) -> Vec<Vec<i32>> {
    let mut permutations = HashSet::new();
    recurse(&mut permutations, vec![], &nums);
    permutations.into_iter().collect()
}


fn recurse(solution: &mut HashSet<Vec<i32>>, permutation: Vec<i32>, remaining: &[i32]) {
    if remaining.is_empty() {
        solution.insert(permutation);
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