# [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

## Problem

Given an integer array `nums`, find a contiguous non-empty subarray within the
array that has the largest product, and return the product.

It is guaranteed that the answer will fit in a 32-bit integer.

A subarray is a contiguous subsequence of the array.

#### Constraints

* `1 <= nums.length <= 2 * 10^4`
* `-10 <= nums[i] <= 10`
* The product of any prefix or suffix of `nums` is guaranteed to fit in a 32-bit
  integer.

#### Examples

```text
Input: nums = [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```

```text
Input: nums = [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
```

## Solution

```rust
// Kadane's algorithm
pub fn max_product(nums: Vec<i32>) -> i32 {
    let mut best = nums[0];
    let mut local = 0;

    for n in nums.iter().copied() {
        local = if local == 0 { 1 } else { local };
        local *= n;
        best = best.max(local);
    }

    local = 0;
    for n in nums.iter().copied().rev() {
        local = if local == 0 { 1 } else { local };
        local *= n;
        best = best.max(local);
    }

    best
}
```

Or with one loop:

```rust
// Kadane's algorithm
pub fn max_product(nums: Vec<i32>) -> i32 {
    let mut best = nums[0];
    let mut l = 0;
    let mut r = 0;

    for i in 0..nums.len() {
        l = if l == 0 { 1 } else { l };
        r = if r == 0 { 1 } else { r };

        l *= nums[i];
        r *= nums[nums.len() - i - 1];

        best = best.max(l).max(r);
    }

    best
}
```