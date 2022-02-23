# [442. Find All Duplicates in an Array](https://leetcode.com/problems/find-all-duplicates-in-an-array/)

## Problem

Given an integer array `nums` of length `n` where all the integers of `nums` are
in the range `[1, n]` and each integer appears once or twice, return an array of
all the integers that appears twice.

You must write an algorithm that runs in O(n) time and uses only constant extra
space.

#### Constraints

* `n == nums.length`
* `1 <= n <= 10^5`
* `1 <= nums[i] <= n`
* Each element in `nums` appears **once** or **twice**.

#### Examples

```text
Input: nums = [4,3,2,7,8,2,3,1]
Output: [2,3]
```

```text
Input: nums = [1,1,2]
Output: [1]
```

```text
Input: nums = [1]
Output: []
```

## Solutions

### Sorting the array in O(n) time

```rust
pub fn find_duplicates(mut nums: Vec<i32>) -> Vec<i32> {
    for idx in 0..nums.len() {
        while nums[idx] > 0 {
            let pos = (nums[idx] - 1) as usize;
            if nums[pos] > 0 {
                nums[idx] = nums[pos];
                nums[pos] = -1;
            } else {
                nums[idx] = 0;
                nums[pos] -= 1;
            }
        }
    }

    let mut result = vec![];
    for (idx, n) in nums.into_iter().enumerate() {
        if n == -2 {
            result.push((idx + 1) as i32);
        }
    }
    result
}
```

### Flipping the sign of the numbers

Instead of sorting the array, we can just set `nums[idx] = -nums[idx]`
if `nums[idx]` where `idx` is a number in the array. Thus, the indexes
corresponding to the numbers that appear only once will contain negative
numbers. The indexes corresponding to numbers that appear twice will contain
positive numbers (once flipped to negative, then to positive).

```rust
pub fn find_duplicates(mut nums: Vec<i32>) -> Vec<i32> {
    let mut result = vec![];
    for idx in 0..nums.len() {
        let pos = nums[idx].abs() as usize - 1;
        nums[pos] = -nums[pos];

        // we need to add the numbers to the result here, because at the and
        // the numbers that are located at indexes that do not appear in `num`
        // will also be positive
        if nums[pos] > 0 {
            result.push(pos as i32 + 1)
        }
    }

    result
}
```