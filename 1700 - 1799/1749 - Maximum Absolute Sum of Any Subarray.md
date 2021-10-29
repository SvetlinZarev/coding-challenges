# [1749. Maximum Absolute Sum of Any Subarray](https://leetcode.com/problems/maximum-absolute-sum-of-any-subarray/)

## Problem

You are given an integer array `nums`. The absolute sum of a
subarray `[numsl, numsl+1, ..., numsr-1, numsr]`
is `abs(numsl + numsl+1 + ... + numsr-1 + numsr)`.

Return the maximum absolute sum of any (possibly empty) subarray of `nums`.

Note that `abs(x)` is defined as follows:

* If `x` is a negative integer, then `abs(x) = -x`.
* If `x` is a non-negative integer, then `abs(x) = x`.

#### Constraints

* `1 <= nums.length <= 10^5`
* `-10^4 <= nums[i] <= 10^4`

#### Examples

```text
Input: nums = [1,-3,2,3,-4]
Output: 5
Explanation: The subarray [2,3] has absolute sum = abs(2+3) = abs(5) = 5.
```

````text
Input: nums = [2,-5,1,-4,3,-2]
Output: 8
Explanation: The subarray [-5,1,-4] has absolute sum = abs(-5+1-4) = abs(-8) = 8.
````

## Solutions

### Using Kadane's algorithm

We need to do two iterations - one to find the maximum sum and one to find the
minimum sum. The answer is the larger absolute value of the min/max sum:

```rust
ub fn max_absolute_sum(nums: Vec<i32>) -> i32 {
    //find the maximum sum
    let mut sum = 0;
    let mut maximum = i32::MIN;

    for &x in nums.iter() {
        sum += x;
        maximum = maximum.max(sum);

        if sum < 0 {
            sum = 0;
        }
    }

    // find the minimum sum
    let mut sum = 0;
    let mut minimum = i32::MAX;

    for &x in nums.iter() {
        sum += x;
        minimum = minimum.min(sum);

        if sum > 0 {
            sum = 0;
        }
    }

    minimum.abs().max(maximum)
}
```

Or with only one loop:

```rust
pub fn max_absolute_sum(nums: Vec<i32>) -> i32 {
    let mut maximum = i32::MIN;
    let mut minimum = i32::MAX;

    let mut psum = 0; // positive sum
    let mut nsum = 0; // negative sum

    for &x in nums.iter() {
        psum += x;
        nsum += x;

        maximum = maximum.max(psum);
        minimum = minimum.min(nsum);

        if psum < 0 {
            psum = 0;
        }

        if nsum > 0 {
            nsum = 0;
        }
    }

    minimum.abs().max(maximum)
}
```

### Using min/max prefix sums:

* If the array contains only negative numbers, the maximum prefix sum will be 0
  thus the result will be `0 - negative_sum`
* If the array contains only positive numbers, the minimum prefix sum will be 0
  thus the result will be `positive_sum - 0`
* Otherwise, the elements of the prefix sums cancel each other:
    * let's have the array `[-2, 3, -5, 7]`, the maximum prefix sum
      is `-2 + 3 - 5 + 7 = 3`, the minimum prefix sum is `-2 + 3 - 5 = -4`, thus
      their difference
      is `(-2 +3 - 5 + 7) - (-2 + 3 - 5) = - 2 + 3 - 5 + 7 + 2 - 3 + 5 = 7`

```rust
pub fn max_absolute_sum(nums: Vec<i32>) -> i32 {
    let mut maximum = 0;
    let mut minimum = 0;
    let mut prefix_sum = 0;

    for &x in nums.iter() {
        prefix_sum += x;

        maximum = maximum.max(prefix_sum);
        minimum = minimum.min(prefix_sum);
    }

    maximum - minimum
}
```

## Related problems

* [53. Maximum Subarray](/000%20-%20099/53%20-%20Maximum%20Subarray.md)