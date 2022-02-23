# [169. Majority Element](https://leetcode.com/problems/majority-element/)

## Problem

Given an array `nums` of size `n`, return the majority element.

The majority element is the element that appears more than `⌊n / 2⌋` times. You
may assume that the majority element always exists in the array.

#### Constraints

* `n == nums.length`
* `1 <= n <= 5 * 10^4`
* `-2^31 <= nums[i] <= 2^31 - 1`

#### Examples

```text
Input: nums = [3,2,3]
Output: 3
```

```text
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

## Solution

```rust
// Boyer-Moore majority voting algorithm
pub fn majority_element(nums: Vec<i32>) -> i32 {
    let mut v = 0;
    let mut vc = 0;

    for n in nums.iter().copied() {
        if v == n {
            vc += 1;
        } else if vc == 0 {
            v = n;
            vc += 1;
        } else {
            vc -= 1;
        }
    }

    v
}
```

## Resources

* [Boyer-Moore Voting algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)