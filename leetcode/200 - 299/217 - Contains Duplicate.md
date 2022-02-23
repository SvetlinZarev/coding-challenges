# [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

## Problem

Given an integer array `nums`, return `true` if any value appears at least twice
in the array, and return `false` if every element is distinct.

#### Constraints

* `1 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`

#### Examples

```text
Input: nums = [1,2,3,1]
Output: true
```

```text
Input: nums = [1,2,3,4]
Output: false
```

## Solutions

### With Sorting - O(n log n)

```rust
pub fn contains_duplicate(mut nums: Vec<i32>) -> bool {
    nums.sort_unstable();
    nums.windows(2).any(|w| w[0] == w[1])
}
```

### With HashMap

Just count how many times each element appears in the array using a HashMap.