# [1679. Max Number of K-Sum Pairs](https://leetcode.com/problems/max-number-of-k-sum-pairs/)

## Problem

### Description

You are given an integer array `nums` and an integer `k`.

In one operation, you can pick two numbers from the array whose sum equals `k`
and remove them from the array.

Return the maximum number of operations you can perform on the array.

### Constraints

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^9`
* `1 <= k <= 10^9`

### Examples

```text
Input: nums = [1,2,3,4], k = 5
Output: 2
Explanation: Starting with nums = [1,2,3,4]:
- Remove numbers 1 and 4, then nums = [2,3]
- Remove numbers 2 and 3, then nums = []
There are no more pairs that sum up to 5, hence a total of 2 operations.
```

```text
Input: nums = [3,1,3,4,3], k = 6
Output: 1
Explanation: Starting with nums = [3,1,3,4,3]:
- Remove the first two 3's, then nums = [1,4,3]
There are no more pairs that sum up to 6, hence a total of 1 operation.
```

### Hints

* The abstract problem asks to count the number of disjoint pairs with a given
  sum `k`.
* For each possible value `x`, it can be paired up with `k - x`.
* The number of such pairs equals to `min(count(x), count(k-x))`, unless
  that `x = k / 2`, where the number of such pairs will be `floor(count(x) / 2)`

## Solutions

This is essentially the "Two Sum" problem

### Two pointers: `O(n log n)`

```rust
use std::cmp::Ordering;

pub fn max_operations(mut nums: Vec<i32>, k: i32) -> i32 {
    assert!(nums.len() > 0);
    nums.sort_unstable();

    let mut l = 0;
    let mut r = nums.len() - 1;
    let mut count = 0;

    while l < r {
        match (nums[l] + nums[r]).cmp(&k) {
            Ordering::Less => {
                l += 1;
            }
            Ordering::Greater => {
                r -= 1;
            }
            Ordering::Equal => {
                count += 1;
                l += 1;
                r -= 1;
            }
        }
    }

    count
}
```

### Using HashMap: `O(n)`

```rust
use std::collections::HashMap;

pub fn max_operations(nums: Vec<i32>, k: i32) -> i32 {
    assert!(nums.len() > 0);

    let mut freq = HashMap::new();
    nums.iter()
        .copied()
        .for_each(|x| *freq.entry(x).or_insert(0) += 1);

    let mut answer = 0;

    for x in nums.iter().copied() {
        let remaining = k - x;

        // There is a corner case where `X == K-X`. In that case we need to 
        // subtract 2 instead of 1 from the key count
        if remaining == x {
            if let Some(count) = freq.get_mut(&x) {
                if *count >= 2 {
                    *count -= 2;
                    answer += 1;
                }
            }
        } else {
            let x_count = *freq.get(&x).unwrap_or(&0);
            if x_count > 0 {
                if let Some(rem_count) = freq.get_mut(&remaining) {
                    if *rem_count > 0 {
                        *rem_count -= 1;
                        *(freq.get_mut(&x).unwrap()) -= 1;
                        answer += 1;
                    }
                }
            }
        }
    }

    answer
}
```

## Related Problems

* [1. Two Sum](/leetcode/000%20-%20099/1%20-%20Two%20Sum.md)
