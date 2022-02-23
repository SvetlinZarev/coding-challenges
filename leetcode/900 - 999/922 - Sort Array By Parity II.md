# [922. Sort Array By Parity II](https://leetcode.com/problems/sort-array-by-parity-ii/)

## Problem

Given an array of integers `nums`, half of the integers in `nums` are odd, and
the other half are even.

Sort the array so that whenever `nums[i]` is odd, `i` is odd, and
whenever `nums[i]` is even, `i` is even.

Return any answer array that satisfies this condition.

#### Constraints

* `2 <= nums.length <= 2 * 10^4`
* `nums.length` is even.
* Half of the integers in `nums` are even.
* `0 <= nums[i] <= 1000`

#### Examples

```text
Input: nums = [4,2,5,7]
Output: [4,5,2,7]
Explanation: [4,7,2,5], [2,5,4,7], [2,7,4,5] would also have been accepted.
```

```
Input: nums = [2,3]
Output: [2,3]
```

## Solution

```rust
pub fn sort_array_by_parity_ii(mut nums: Vec<i32>) -> Vec<i32> {
    if nums.len() <= 1 {
        return nums;
    }

    let mut even = 0;
    let mut odd = 1;

    while even < nums.len() && odd < nums.len() {
        if nums[even] % 2 != 0 {
            while odd < nums.len() {
                if nums[odd] % 2 == 0 {
                    break;
                }

                odd += 2;
            }

            nums.swap(even, odd);
        }
        even += 2;
    }

    nums
}
```