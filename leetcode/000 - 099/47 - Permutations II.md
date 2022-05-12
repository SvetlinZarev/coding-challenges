# [47. Permutations II](https://leetcode.com/problems/permutations-ii/)

## Problem

### Description

Given a collection of numbers, `nums`, that might contain duplicates, return all
possible unique permutations in any order

### Constraints

* `1 <= nums.length <= 8`
* `-10 <= nums[i] <= 10`

### Examples

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

## Solutions

### Backtracking + HashMap to filter out the duplicates

* Because the length of `nums` is at most 8, we can use a bitmask to track the
  used elements instead of copying the whole list as in the previous
  implementation

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

### Backtracking + Sorting to filter out the duplicates

```rust
pub fn permute_unique(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
    assert!(nums.len() >= 1);
    assert!(nums.len() <= 8);

    // Sort the numbers in order to be able to avoid the duplicates
    nums.sort_unstable();

    let mut answer = vec![];
    let mut permutation = vec![];
    backtrack(&mut answer, &mut permutation, &nums, 0);

    answer
}

fn backtrack(answer: &mut Vec<Vec<i32>>, permutation: &mut Vec<i32>, nums: &[i32], mut used: u32) {
    if permutation.len() == nums.len() {
        answer.push(permutation.clone());
        return;
    }

    for i in 0..nums.len() {
        // Skip used numbers
        if used & 1 << i != 0 {
            continue;
        }

        // Skip duplicate permutations
        //
        // IF:
        // * has a previous element
        // * that previous element is the same as the current element
        // * that previous element is not used
        if i > 0 && nums[i - 1] == nums[i] && used & (1 << (i - 1)) == 0 {
            continue;
        }

        used |= 1 << i;
        permutation.push(nums[i]);
        backtrack(answer, permutation, nums, used);
        permutation.pop();
        used ^= 1 << i;
    }
}
```

## Related Problems

* [46. Permutations](46%20-%20Permutations.md)
