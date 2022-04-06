# [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

## Problem

### Description

Given a 1-indexed array of integers numbers that is already sorted in
non-decreasing order, find two numbers such that they add up to a specific
target number. Let these two numbers be `numbers[index1]` and `numbers[index2]`
where `1 <= first < second <= numbers.length`.

Return the indices of the two numbers, `index1` and `index2`, as an integer
array `[index1, index2]` of length 2.

The tests are generated such that there is exactly one solution. You may not use
the same element twice.

### Constraints

* `2 <= numbers.length <= 3 * 1^04`
* `-1000 <= numbers[i] <= 1000`
* `-1000 <= target <= 1000`
* `numbers` is sorted in non-decreasing order.

### Examples

```text
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

```text
Input: numbers = [2,3,4], target = 6
Output: [1,3]
Explanation: The sum of 2 and 4 is 6. Therefore index1 = 1, index2 = 3.
```

```text
Input: numbers = [-1,0], target = -1
Output: [1,2]
Explanation: The sum of -1 and 0 is -1. Therefore index1 = 1, index2 = 2.
```

## Solution

```rust
pub fn two_sum(numbers: Vec<i32>, target: i32) -> Vec<i32> {
    let mut a = 0;
    let mut b = numbers.len() - 1;

    while a < b {
        let sum = numbers[a] + numbers[b];
        if sum == target {
            break;
        }

        if sum < target {
            a += 1;
        } else {
            b -= 1;
        }
    }

    vec![a as i32 + 1, b as i32 + 1]
}
```

## Related Problems

* [15. 3Sum](/leetcode/000%20-%20099/15%20-%203Sum.md)
* [923. 3Sum With Multiplicity](/leetcode/900%20-%20999/923%20-%203Sum%20With%20Multiplicity.md)