# [41. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

## Problem

Given an unsorted integer array `nums`, return the smallest missing positive
integer.

You must implement an algorithm that runs in `O(n)` time and uses constant extra
space.

#### Constraints

* `1 <= nums.length <= 5 * 10^5`
* `-2^31 <= nums[i] <= 2^31 - 1`

#### Examples

```text
Input: nums = [1,2,0]
Output: 3
```

```text
Input: nums = [3,4,-1,1]
Output: 2
```

```text
Input: nums = [7,8,9,11,12]
Output: 1
```

## Solution

#### Observations

1. If the array contains all positive numbers in the range `[1; n]`, then the
   answer is `n+1`
2. If the array contains at least one duplicate number in the range `[1; n]`
   then there is a gap in the range `[1; n]`, thus the answer is somewhere in
   that range
3. If the array contains at least one number larger than `n`, then there is a
   gap in the range `[1; n]`, thus the answer is somewhere in that range
4. If the array contains at least one number less than or equal to zero, then
   there is a gap in the range `[1; n]`, thus the answer is somewhere in that
   range

Given those observations we can conclude that the answer lies in `[1; n]` unless
that range contains all numbers from `1` to `n`, in which case the answer
is `n + 1`.

#### Solution

We can use the above observations to construct a very simple solution. We should
iterate over the array once, swapping the elements to their correct position in
the array. That position is `value - 1`, because the array is 0-based. The
answer is `index + 1` where `index` is the first position
where `index + 1 != array[index]`

```rust
pub fn first_missing_positive(mut nums: Vec<i32>) -> i32 {
    let len = nums.len() as i32;

    let mut idx = 0;
    while idx < nums.len() {
        let position = (nums[idx] - 1) as usize;

        if nums[idx] <= 0 || nums[idx] > len || nums[idx] == nums[position] as i32 {
            idx += 1;
            continue;
        }

        nums.swap(idx, position);
    }

    for idx in 0..nums.len() {
        if nums[idx] != (idx + 1) as i32 {
            return (idx + 1) as i32;
        }
    }

    len + 1
}
```