# [16. 3Sum Closest](https://leetcode.com/problems/3sum-closest/)

## Problem

Given an integer array `nums` of length `n` and an integer `target`, find three
integers in `nums` such that the sum is closest to `target`.

Return the sum of the three integers.

You may assume that each input would have exactly one solution.

#### Constraints

* `3 <= nums.length <= 1000`
* `-1000 <= nums[i] <= 1000`
* `-10^4 <= target <= 10^4`

#### Examples

```text
Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

## Solution

```rust
 pub fn three_sum_closest(mut nums: Vec<i32>, target: i32) -> i32 {
    nums.sort_unstable();

    let mut result = 0;
    let mut diff = i32::MAX;

    for i in 0..nums.len().saturating_sub(2) {
        let a = nums[i];

        let mut j = i + 1;
        let mut k = nums.len() - 1;

        while j < k {
            let b = nums[j];
            let c = nums[k];
            let sum = a + b + c;
            if (target - sum).abs() < diff {
                diff = (target - sum).abs();
                result = sum;
            }

            if sum == target {
                return target;
            } else if sum > target {
                k -= 1;
            } else {
                j += 1;
            }
        }
    }

    result
}
```