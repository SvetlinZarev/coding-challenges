# [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/)

## Problem

Given an integer array `nums`, move all `0`'s to the end of it while maintaining
the relative order of the non-zero elements.

Note that you must do this in-place without making a copy of the array.

#### Constraints

* `1 <= nums.length <= 10^4`
* `-2^31 <= nums[i] <= 2^31 - 1`

#### Examples

```text
Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

```text
Input: nums = [0]
Output: [0]
```

## Solution

```rust
pub fn move_zeroes(nums: &mut Vec<i32>) {
    let mut a = 0;
    let mut b = 0;

    'all: while b < nums.len() {
        while nums[b] == 0 {
            b += 1;
            if b >= nums.len() {
                break 'all;
            }
        }

        nums.swap(a, b);
        a += 1;
        b += 1;
    }
}
```