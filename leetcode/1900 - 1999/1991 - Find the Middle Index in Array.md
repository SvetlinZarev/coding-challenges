# [1991. Find the Middle Index in Array](https://leetcode.com/problems/find-the-middle-index-in-array/)

## Problem

### Description

Given a 0-indexed integer array `nums`, find the leftmost `middleIndex` (i.e.,
the smallest amongst all the possible ones).

A `middleIndex` is an index
where `nums[0] + nums[1] + ... + nums[middleIndex-1] == nums[middleIndex+1] + nums[middleIndex+2] + ... + nums[nums.length-1]`

If `middleIndex == 0`, the left side sum is considered to be `0`. Similarly,
if `middleIndex == nums.length - 1`, the right side sum is considered to be `0`.

Return the leftmost `middleIndex` that satisfies the condition, or `-1` if there
is no such index.

### Constraints

* `1 <= nums.length <= 100`
* `-1000 <= nums[i] <= 1000`

### Examples

```text
Input: nums = [2,3,-1,8,4]
Output: 3
Explanation: The sum of the numbers before index 3 is: 2 + 3 + -1 = 4
The sum of the numbers after index 3 is: 4 = 4
```

```text
Input: nums = [1,-1,4]
Output: 2
Explanation: The sum of the numbers before index 2 is: 1 + -1 = 0
The sum of the numbers after index 2 is: 0
```

```text
Input: nums = [2,5]
Output: -1
Explanation: There is no valid middleIndex.
```

## Solutions

### Prefix sum (with additional memory)

```rust
const NO_SUCH_INDEX: i32 = -1;

pub fn find_middle_index<N: AsRef<[i32]>>(nums: N) -> i32 {
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

pub fn find_middle_index<N: AsRef<[i32]>>(nums: N) -> i32 {
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

## Related Problems

* [724. Find Pivot Index](/leetcode/700%20-%20799/724%20-%20Find%20Pivot%20Index.md)
