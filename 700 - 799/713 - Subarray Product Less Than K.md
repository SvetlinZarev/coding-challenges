# [713. Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/solution/)

## Problem

Given an array of integers `nums` and an integer `k`, return the number of
contiguous subarrays where the product of all the elements in the subarray is
strictly less than `k`.

#### Constraints

* `1 <= nums.length <= 3 * 10^4`
* `1 <= nums[i] <= 1000`
* `0 <= k <= 1066`

#### Examples

```text
Input: nums = [10,5,2,6], k = 100
Output: 8

Explanation: The 8 subarrays that have product less than 100 are:
[10], [5], [2], [6], [10, 5], [5, 2], [2, 6], [5, 2, 6]
Note that [10, 5, 2] is not included as the product of 100 is not strictly less than k.
```

```text
Input: nums = [1,2,3], k = 0
Output: 0
```

## Solution

```rust
pub fn num_subarray_product_less_than_k(nums: Vec<i32>, k: i32) -> i32 {
    let mut count = 0;

    let mut from = 0;
    let mut to = 0;

    let mut product = 1;
    while to < nums.len() {
        product *= nums[to];
        to += 1;

        while from < to && product >= k {
            product /= nums[from];
            from += 1;
        }

        // The number of subarrays that include the rightmost number is (right - left)
        count += (to - from) as i32;
    }

    count
}
```