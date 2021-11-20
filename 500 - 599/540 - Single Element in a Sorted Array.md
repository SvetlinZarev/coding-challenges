# [540. Single Element in a Sorted Array](https://leetcode.com/problems/single-element-in-a-sorted-array/)

## Problem

You are given a sorted array consisting of only integers where every element
appears exactly twice, except for one element which appears exactly once.

Return the single element that appears only once.

Your solution must run in O(log n) time and O(1) space.

#### Constraints

* `1 <= nums.length <= 10^5`
* `0 <= nums[i] <= 10^5`

#### Examples

```text
Input: nums = [1,1,2,3,3,4,4,8,8]
Output: 2
```

```text
Input: nums = [3,3,7,7,10,11,11]
Output: 10
```

## Solution

**Observations:**

* Because each number appears twice, one will be on an even index, while the
  other will be on an odd index: `[1, 1]` -> `idx0 => 1`, `idx1 => 1`
* Therefore, we can apply a binary search strategy:
    * If our `mid` index is even, then the duplicate number must be `mid + 1`.
      If this is true, then all numbers until `mid` appear twice, thus the
      missing number is in the other half of the array.
    * The same logic applies if our `mid` index is odd, we would expect that its
      corresponding duplicate to be at `mid - 1`. If this is true, al number
      until mid appear twice and the missing number os in the other half of the
      array

```rust
pub fn single_non_duplicate(nums: Vec<i32>) -> i32 {
    let mut lo = 0;
    let mut hi = nums.len() - 1;

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;

        if mid % 2 == 0 {
            if nums[mid] == nums[mid + 1] {
                // if `nums[mid] == nums[mid + 1]`, then we know that
                // both numbers are the same, so we don't need to
                // check them again, thus `mid + 2`
                lo = mid + 2;
            } else {
                hi = mid;
            }
        } else {
            if nums[mid - 1] == nums[mid] {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
    }

    return nums[lo];
}
```
