# [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

## Problem

Given a collection of candidate numbers (`candidates`) and a target
number (`target`), find all unique combinations in `candidates` where the
candidate numbers sum to `target`.

Each number in `candidates` may only be used once in the combination.

Note: The solution set must not contain duplicate combinations.

#### Constraints

* `1 <= candidates.length <= 100`
* `1 <= candidates[i] <= 50`
* `1 <= target <= 30`

#### Examples

```text
Input: candidates = [10,1,2,7,6,1,5], target = 8
Output: 
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

```text
Input: candidates = [2,5,2,1,2], target = 5
Output: 
[
[1,2,2],
[5]
]
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
    let mut idx = 0;
    while idx < nums.len() {
        let n = nums[idx];
        if n > target {
            return; // the array is sorted in increasing order
        }

        // Avoid duplicates, we have already processed that number from the backtracking bellow
        if idx > 0 && n == nums[idx - 1] {
            idx += 1;
            continue;
        }

        // move to the next number, this will also skip the current
        // number from repeating in the backtracking below
        idx += 1;

        combination.push(n);

        if n == target {
            result.push(combination.clone());
            combination.pop();
            return;
        }

        // shrink the slice in order to avoid duplicates - i.e. `2 + 3 == 3 + 2`
        backtrack(&nums[idx..], result, combination, target - n);

        combination.pop();
    }
}
```

## Related Problems

* [39. Combination Sum](39%20-%20Combination%20Sum.md)
