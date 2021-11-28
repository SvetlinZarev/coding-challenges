# [1567. Maximum Length of Subarray With Positive Product](https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/)

## Problem

Given an array of integers `nums`, find the maximum length of a subarray where
the product of all its elements is positive.

A subarray of an array is a consecutive sequence of zero or more values taken
out of that array.

Return the maximum length of a subarray with positive product.

#### Constraints

* `1 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`

#### Hints

* Split the whole array into subarrays by zeroes since a subarray with positive
  product cannot contain any zero.
* If the subarray has even number of negative numbers, the whole subarray has
  positive product.
* Otherwise, we have two choices, either - remove the prefix till the first
  negative element in this subarray, or remove the suffix starting from the last
  negative element in this subarray.

#### Examples

```text
Input: nums = [1,-2,-3,4]
Output: 4
Explanation: The array nums already has a positive product of 24.
```

```text
Input: nums = [0,1,-2,-3,-4]
Output: 3
Explanation: The longest subarray with positive product is [1,-2,-3] which has a product of 6.
Notice that we cannot include 0 in the subarray since that'll make the product 0 which is not positive.
```

```text
Input: nums = [-1,-2,-3,0,1]
Output: 2
Explanation: The longest subarray with positive product is [-1,-2] or [-2,-3].
```

```text
Input: nums = [-1,2]
Output: 1
```

```text
Input: nums = [1,2,3,5,-6,4,0,10]
Output: 4
```

## Solution

```rust
pub fn get_max_len(nums: Vec<i32>) -> i32 {
    let mut len = 0;

    let mut product = 0;
    let mut start = 0;
    let mut end = 0;

    for (idx, n) in nums.iter().copied().enumerate() {
        product = if product == 0 { 1 } else { product };

        product *= n.signum();
        if product == 0 {
            start = idx + 1;
        } else if product > 0 {
            end = idx + 1;
            len = len.max(end - start);
        }
    }

    product = 0;
    start = 0;
    end = 0;

    for (idx, n) in nums.iter().copied().rev().enumerate() {
        product = if product == 0 { 1 } else { product };

        product *= n.signum();
        if product == 0 {
            start = idx + 1;
        } else if product > 0 {
            end = idx + 1;
            len = len.max(end - start);
        }
    }

    len as i32
}
```