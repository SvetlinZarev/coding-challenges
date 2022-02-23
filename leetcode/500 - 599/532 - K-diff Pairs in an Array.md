# [532. K-diff Pairs in an Array](https://leetcode.com/problems/k-diff-pairs-in-an-array/)

## Problem

Given an array of integers `nums` and an integer `k`, return the number of
unique `k`-diff pairs in the array.

A `k`-diff pair is an integer pair `(nums[i], nums[j])`, where the following are
true:

* `0 <= i < j < nums.length`
* `|nums[i] - nums[j]| == k`

Notice that `|val|` denotes the absolute value of `val`.

#### Constraints

* `1 <= nums.length <= 10^4`
* `-10^7 <= nums[i] <= 10^7`
* `0 <= k <= 10^7`

#### Examples

```text
Input: nums = [3,1,4,1,5], k = 2
Output: 2
Explanation: There are two 2-diff pairs in the array, (1, 3) and (3, 5).
Although we have two 1s in the input, we should only return the number of unique pairs.
```

```text
Input: nums = [1,2,3,4,5], k = 1
Output: 4
Explanation: There are four 1-diff pairs in the array, (1, 2), (2, 3), (3, 4) and (4, 5).
```

```text
Input: nums = [1,3,1,5,4], k = 0
Output: 1
Explanation: There is one 0-diff pair in the array, (1, 1).
```

## Solution

### O(n) solution with HashMap

```rust
use std::collections::HashMap;

pub fn find_pairs(nums: Vec<i32>, k: i32) -> i32 {
    let mut diff = HashMap::new();
    nums.iter().copied().for_each(|n| {
        diff.entry(n - k).and_modify(|v| *v = true).or_insert(false);
    });

    let mut count = 0;
    nums.iter().for_each(|n| {
        let status = diff.remove(n);
        if let Some(duplicate) = status {
            // if k != 0 {
            //     count += 1;
            // } else if duplicate {
            // This branch is relevant only when K=0
            //     count += 1;
            // }

            // or with a one-liner
            if k != 0 || duplicate {
                count += 1;
            }
        }
    });

    count
}
```

### O(n log n) solution using sorting (similar to 2-sum)

```rust
use std::cmp::Ordering;

pub fn find_pairs(mut nums: Vec<i32>, k: i32) -> i32 {
    if nums.len() < 2 {
        return 0;
    }
    nums.sort_unstable();

    let mut count = 0;
    let mut a = 0;
    let mut b = 1;

    while b < nums.len() && a < b {
        match (nums[b] - nums[a]).cmp(&k) {
            Ordering::Equal => {
                count += 1;

                let prev = nums[a];
                while nums[a] == prev && a < b {
                    a += 1;
                }

                let mut prev = nums[b];
                while b < nums.len() && nums[b] == prev {
                    b += 1;
                }
            }

            Ordering::Less => {
                b += 1;
            }

            Ordering::Greater => {
                if a + 1 < b {
                    a += 1;
                } else {
                    a += 1;
                    b += 1;
                }
            }
        }
    }

    count
}
```