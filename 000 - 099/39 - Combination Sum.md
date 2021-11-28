# [39. Combination Sum](https://leetcode.com/problems/combination-sum/)

## Problem

Given an array of distinct integers `candidates` and a target integer `target`,
return a list of all unique combinations of `candidates` where the chosen
numbers sum to `target`. You may return the combinations in any order.

The same number may be chosen from `candidates` an unlimited number of times.
Two combinations are unique if the frequency of at least one of the chosen
numbers is different.

It is guaranteed that the number of unique combinations that sum up to `target`
is less than 150 combinations for the given input.

#### Constraints

* `1 <= candidates.length <= 30`
* `1 <= candidates[i] <= 200`
* All elements of `candidates` are distinct.
* `1 <= target <= 500`

#### Examples

```text
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]

Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```

```text
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

```text
Input: candidates = [2], target = 1
Output: []
```

```text
Input: candidates = [1], target = 1
Output: [[1]]
```

```text
Input: candidates = [1], target = 2
Output: [[1,1]]
```

## Solution

```rust
pub fn combination_sum(mut candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
    candidates.sort_unstable(); // need a sorted array so we can do an early exit later

    let mut result = vec![];
    let mut buffer = vec![];

    backtrack(&candidates, &mut result, &mut buffer, target);

    result
}

fn backtrack(nums: &[i32], result: &mut Vec<Vec<i32>>, combination: &mut Vec<i32>, target: i32) {
    nums.iter()
        .copied()
        .take_while(|&n| n <= target)
        .enumerate()
        .for_each(|(idx, n)| {
            combination.push(n);

            if n == target {
                result.push(combination.clone());
                combination.pop();
                return;
            }

            // shrink the slice in order to avoid duplicates - i.e. `2 + 3 == 3 + 2`
            backtrack(&nums[idx..], result, combination, target - n);
            combination.pop();
        });
}
```