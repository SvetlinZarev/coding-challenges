# [977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/)

## Problem

Given an integer array `nums` sorted in non-decreasing order, return an array of
the squares of each number sorted in non-decreasing order.

#### Constraints

* `1 <= nums.length <= 10^4`
* `-10^4 <= nums[i] <= 10^4`
* `nums` is sorted in non-decreasing order.

#### Examples

```text
Input: nums = [-4,-1,0,3,10]
Output: [0,1,9,16,100]
Explanation: After squaring, the array becomes [16,1,0,9,100].
After sorting, it becomes [0,1,9,16,100].
```

```text
Input: nums = [-7,-3,2,3,11]
Output: [4,9,9,49,121]
```

## Solution

```rust
pub fn sorted_squares(nums: Vec<i32>) -> Vec<i32> {
    let mut result = Vec::with_capacity(nums.len());

    let mut a = nums
        .binary_search(&0)
        .map(|v| Some(v))
        .map_err(|v| v.checked_sub(1))
        .unwrap_or_else(|e| e);
    let mut b = a.map(|a| a + 1).unwrap_or(0);

    while b < nums.len() {
        let aa = match a {
            None => break,
            Some(a) => a,
        };

        if nums[aa].abs() <= nums[b].abs() {
            result.push(nums[aa] * nums[aa]);
            a = aa.checked_sub(1);
        } else {
            result.push(nums[b] * nums[b]);
            b += 1;
        }
    }

    while let Some(aa) = a {
        if aa >= nums.len() {
            break;
        }

        result.push(nums[aa] * nums[aa]);
        a = aa.checked_sub(1);
    }

    while b < nums.len() {
        result.push(nums[b] * nums[b]);
        b += 1;
    }

    result
}
```