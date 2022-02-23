# [229. Majority Element II](https://leetcode.com/problems/majority-element-ii/)

## Problem

Given an integer array of size `n`, find all elements that appear more
than  `n/3` times.

#### Constraints

* `1 <= nums.length <= 5 * 10^4`
* `-10^9 <= nums[i] <= 10^9`

#### Examples

```text
Input: nums = [3,2,3]
Output: [3]
```

```text
Input: nums = [1]
Output: [1]
```

```text
Input: nums = [1,2]
Output: [1,2]
```

## Solution

### [Boyer–Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)

```rust
pub fn majority_element(nums: Vec<i32>) -> Vec<i32> {
    let (mut a, mut ac) = (0, 0);
    let (mut b, mut bc) = (0, 0);

    for n in nums.iter().copied() {
        if n == a {
            ac += 1;
        } else if n == b {
            bc += 1;
        } else if ac == 0 {
            a = n;
            ac = 1;
        } else if bc == 0 {
            b = n;
            bc = 1;
        } else {
            ac -= 1;
            bc -= 1;
        }
    }

    ac = 0;
    bc = 0;
    for n in nums.iter().copied() {
        if n == a {
            ac += 1;
        } else if n == b {
            bc += 1;
        }
    }

    let mut result = Vec::with_capacity(2);
    if ac > nums.len() / 3 {
        result.push(a);
    }
    if bc > nums.len() / 3 {
        result.push(b);
    }
    result
}
```

### Using Hashmap

```rust
use std::collections::HashMap;

pub fn majority_element(nums: Vec<i32>) -> Vec<i32> {
    let mut freq = HashMap::with_capacity(16.max(nums.len() / 3));

    for n in nums.iter().copied() {
        freq.entry(n).and_modify(|f| *f += 1).or_insert(1);
    }

    freq.iter()
        .filter(|(_, &v)| v > nums.len() / 3)
        .map(|(&k, _)| k)
        .collect()
}

```