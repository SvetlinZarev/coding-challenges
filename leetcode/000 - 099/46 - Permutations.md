# [46. Permutations](https://leetcode.com/problems/permutations/)

## Problem

### Description

Given an array `nums` of distinct integers, return all the possible
permutations. You can return the answer in any order.

### Constraints

* `1 <= nums.length <= 6`
* `-10 <= nums[i] <= 10`
* All the integers of `nums` are unique.

### Examples

```text
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

```text
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

## Solutions

### Backtracking #1

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

### Backtracking #2

Instead of copying the list of remaining numbers on every iteration we can use
bitmask to track which numbers have been used. This is possible because the
number of elements is low (in this case - at most 6)

```rust
pub fn permute(nums: Vec<i32>) -> Vec<Vec<i32>> {
    assert!(nums.len() >= 1);
    assert!(nums.len() <= 6);

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

        used |= 1 << i;
        permutation.push(nums[i]);
        backtrack(answer, permutation, nums, used);
        permutation.pop();
        used ^= 1 << i;
    }
}
```

## Related Problems

* [47. Permutations II](47%20-%20Permutations%20II.md)
