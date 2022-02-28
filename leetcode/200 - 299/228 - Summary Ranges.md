# [228. Summary Ranges](https://leetcode.com/problems/summary-ranges/)

## Problem

### Description

You are given a sorted unique integer array `nums`.

Return the smallest sorted list of ranges that cover all the numbers in the
array exactly. That is, each element of `nums` is covered by exactly one of the
ranges, and there is no integer `x` such that `x` is in one of the ranges but
not in `nums`.

Each range `[a,b]` in the list should be output as:

* `a->"` if `a != b`
* `a` if `a == b`

### Constraints

* `0 <= nums.length <= 20`
* `-2^31 <= nums[i] <= 2^31 - 1`
* All the values of `nums` are unique.
* `nums` is sorted in ascending order.

### Examples

```Input: nums = [0,1,2,4,5,7]
Output: ["0->2","4->5","7"]
Explanation: The ranges are:
[0,2] --> "0->2"
[4,5] --> "4->5"
[7,7] --> "7"
```

```text
Input: nums = [0,2,3,4,6,8,9]
Output: ["0","2->4","6","8->9"]
Explanation: The ranges are:
[0,0] --> "0"
[2,4] --> "2->4"
[6,6] --> "6"
[8,9] --> "8->9"
```

## Solutions

```rust
pub fn summary_ranges(nums: Vec<i32>) -> Vec<String> {
    let mut ranges = vec![];
    if nums.is_empty() {
        return ranges;
    }

    let mut start = nums[0];
    for idx in 1..nums.len() {
        if (nums[idx] - nums[idx - 1]).abs() > 1 {
            let range = if nums[idx - 1] == start {
                format!("{start}")
            } else {
                format!("{start}->{}", nums[idx - 1])
            };

            ranges.push(range);
            start = nums[idx];
        }
    }

    // handle the last element of the array - i.e. - the last range
    let range = if nums[nums.len() - 1] == start {
        format!("{start}")
    } else {
        format!("{start}->{}", nums[nums.len() - 1])
    };
    ranges.push(range);

    ranges
}
```

Or we can handle the last element inside the loop with help of some
short-circuiting checks:

```rust
pub fn summary_ranges(nums: Vec<i32>) -> Vec<String> {
    let mut ranges = vec![];
    if nums.is_empty() {
        return ranges;
    }

    let mut start = nums[0];
    for idx in 1..=nums.len() {
        if idx == nums.len() || (nums[idx] - nums[idx - 1]).abs() > 1 {
            let range = if nums[idx - 1] == start {
                format!("{start}")
            } else {
                format!("{start}->{}", nums[idx - 1])
            };

            ranges.push(range);
            if idx < nums.len() {
                start = nums[idx];
            }
        }
    }

    ranges
}
```