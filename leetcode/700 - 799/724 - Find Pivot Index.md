# [724. Find Pivot Index](https://leetcode.com/problems/find-pivot-index/)

## Problem

### Description

Given an array of integers `nums`, calculate the pivot index of this array.

The pivot index is the index where the sum of all the numbers strictly to the
left of the index is equal to the sum of all the numbers strictly to the index's
right.

If the index is on the left edge of the array, then the left sum is `0` because
there are no elements to the left. This also applies to the right edge of the
array.

Return the **leftmost** pivot index. If no such index exists, return `-1`.

### Constraints

* `1 <= nums.length <= 10^4`
* `-1000 <= nums[i] <= 1000`

### Examples

```text
Input: nums = [1,7,3,6,5,6]
Output: 3
Explanation:
The pivot index is 3.
Left sum = nums[0] + nums[1] + nums[2] = 1 + 7 + 3 = 11
Right sum = nums[4] + nums[5] = 5 + 6 = 11
```

```text
Input: nums = [1,2,3]
Output: -1
Explanation:
There is no index that satisfies the conditions in the problem statement.
```

```text
Input: nums = [2,1,-1]
Output: 0
Explanation:
The pivot index is 0.
Left sum = 0 (no elements to the left of index 0)
Right sum = nums[1] + nums[2] = 1 + -1 = 0
```

## Solutions

### Prefix sum (with additional memory)

```rust
const NO_SUCH_INDEX: i32 = -1;

pub fn pivot_index<N: AsRef<[i32]>>(nums: N) -> i32 {
    let nums = nums.as_ref();

    let mut psum = vec![0; nums.len()];
    psum[0] = nums[0];

    for idx in 1..psum.len() {
        psum[idx] = psum[idx - 1] + nums[idx];
    }

    for idx in 0..psum.len() {
        let left = if idx == 0 { 0 } else { psum[idx - 1] };
        let right = psum[psum.len() - 1] - psum[idx];
        if left == right {
            return idx as i32;
        }
    }

    NO_SUCH_INDEX
}
```

### Prefix sum (constant memory)

We don't need to keep the prefix sum foreach index. We are only interested in
the prefix sum to the left of our index. THus, we can accumulate it on the fly:

```rust
const NO_SUCH_INDEX: i32 = -1;

pub fn pivot_index<N: AsRef<[i32]>>(nums: N) -> i32 {
    let nums = nums.as_ref();

    // we need the total sum in order tho calculate the sum of the 
    // elements to the right of the index
    let sum = nums.iter().sum::<i32>();

    // constant memory :) we store only the prefix sum to the left of our index
    let mut left_sum = 0;

    for (idx, n) in nums.iter().copied().enumerate() {
        if left_sum == sum - left_sum - n {
            return idx as i32;
        }

        // update the prefix sum
        left_sum += n;
    }

    NO_SUCH_INDEX
}
```

## Related problems

* [1991. Find the Middle Index in Array](/leetcode/1900%20-%201999/1991%20-%20Find%20the%20Middle%20Index%20in%20Array.md)
