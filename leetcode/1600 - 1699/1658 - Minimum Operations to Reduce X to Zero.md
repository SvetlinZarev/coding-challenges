# [1658. Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/)

## Problem

### Description

You are given an integer array `nums` and an integer `x`. In one operation, you
can either remove the leftmost or the rightmost element from the array `nums`
and subtract its value from `x`. Note that this modifies the array for future
operations.

Return the minimum number of operations to reduce `x` to exactly `0` if it is
possible, otherwise, return `-1`.

### Constraints

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^4`
* `1 <= x <= 10^9`

### Examples

```text
Input: nums = [1,1,4,2,3], x = 5
Output: 2
```

> Explanation: The optimal solution is to remove the last two elements to reduce
> x to zero.

```text
Input: nums = [5,6,7,8,9], x = 4
Output: -1
```

```text
Input: nums = [3,2,20,1,1,3], x = 10
Output: 5
```

> Explanation: The optimal solution is to remove the last three elements and the
> first two elements (5 operations in total) to reduce `x` to zero.

## Solutions

### Sliding window

Observation:

* The maximum number of operations we can perform is equal to the number of
  elements in the array
    * The number of elements removed equals `nums.len()` minus the number of
      elements that aren't removed.
    * Thus, to find the minimum number of elements to remove, we can find
      the maximum number of elements to not remove!
* If the sum of the array elements is less than `x`, then we cannot possibly
  reach zero
* Because all elements in `nums` are positive numbers (the prefix sum always
  increases),then if the sum of all elements is equal to `x`, the solution will
  be the number of elements in `nums`
* In all the other cases we need to find the largest sub-array that sums
  to `total_sum - x`:
    * If there is no such sub-array, then we cannot reach exactly zero, thus
      there is no solution
    * Because the number of operations equals to the number of elements, the
      longer sub-array we find, the less operations we need to do

```rust
const NO_SOLUTION: i32 = -1;

pub fn min_operations<A: AsRef<[i32]>>(nums: A, x: i32) -> i32 {
    assert!(x > 0);

    let nums = nums.as_ref();
    let total = nums.iter().sum::<i32>();
    let target = total - x;

    // The array sums to X, which means that the answer is the number of
    // elements in the array (because there are only positive elements
    // in the array!)
    if target == 0 {
        return nums.len() as i32;
    }

    // Cannot reach zero, because the sum of all elements is less than X
    if target < 0 {
        return NO_SOLUTION;
    }

    let mut l = 0;
    let mut r = 0;
    let mut sum = 0;
    let mut max = 0;

    while r < nums.len() {
        sum += nums[r];
        r += 1;

        while sum > target {
            sum -= nums[l];
            l += 1;
        }

        if sum == target {
            max = max.max(r - l);
        }
    }

    // If max == 0, means that there is no such sub-array
    if max > 0 {
        return (nums.len() - max) as i32;
    }

    NO_SOLUTION
}
```

## Related Problems

* [1423. Maximum Points You Can Obtain from Cards](/leetcode/1400%20-%201499/1423%20-%20Maximum%20Points%20You%20Can%20Obtain%20from%20Cards.md)
