# [560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

## Problem

Given an array of integers `nums` and an integer `k`, return the total number
of **continuous** subarrays whose sum equals to `k`.

#### Constraints

* `1 <= nums.length <= 2 * 10^4`
* `-1000 <= nums[i] <= 1000`
* `-10^7 <= k <= 10^7`

#### Examples

```text
Input: nums = [1,1,1], k = 2
Output: 2
```

```text
Input: nums = [1,2,3], k = 3
Output: 2
```

## Solutions

### Bruteforce (`O(n^2)`)

Check all possible sub-array combinations:

```rust
pub fn subarray_sum(nums: Vec<i32>, k: i32) -> i32 {
    let mut count = 0;

    for from in 0..nums.len() {
        let mut sum = 0;
        for y in nums.iter().copied().skip(from) {
            sum += y;
            if sum == k {
                count += 1;
            }
        }
    }

    count
}
```

### Using a HashMap (`O(n)`)

```rust
use std::collections::HashMap;

pub fn subarray_sum(nums: Vec<i32>, k: i32) -> i32 {
    let mut sum_count = HashMap::new();
    sum_count.insert(0, 1);

    let mut count = 0;
    let mut sum = 0;

    for x in nums.iter().copied() {
        sum += x;

        if let Some(&cnt) = sum_count.get(&(sum - k)) {
            count += cnt;
        }

        sum_count.entry(sum).and_modify(|c| *c += 1).or_insert(1);
    }

    count
}
```