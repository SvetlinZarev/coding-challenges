# [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

## Problem

Given an integer array `nums`, return an array answer such that `answer[i]` is
equal to the product of all the elements of `nums` except `nums[i]`.

The product of any prefix or suffix of `nums` is guaranteed to fit in a 32-bit
integer.

You **must** write an algorithm that runs in O(n) time and **without** using the
division operation.

#### Constraints

* `2 <= nums.length <= 10^5`
* `-30 <= nums[i] <= 30`
* The product of any prefix or suffix of `nums` is guaranteed to fit in a 32-bit
  integer.

#### Examples

```text
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

```text
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

#### Follow up

Can you solve the problem in O(1) extra space complexity? (The output array does
not count as extra space for space complexity analysis.)

## Solutions

### Using division

Using division is not allowed in the problem statement. Yet, it can give us
valuable insight into the problem.

We can solve it by calculating the product of all numbers (assuming there are no
zeroes) then divide the product by `nums[idx]` for each index;

### Using prefix and suffix products

It can be easily seen that `(a * b * c) / b == a * c`; Thus we can avoid the
division operation by calculating the "prefix" and "suffix" products for each
index, then the result would be `result[i] = prefix[i] * suffix[i]`. This also
avoids the problem with multiple zeroes in the input array.

```rust
pub fn product_except_self(nums: Vec<i32>) -> Vec<i32> {
    if nums.is_empty() {
        return nums;
    }

    // Calculate the prefix product
    let mut prefix = vec![1; nums.len()];
    for idx in 1..nums.len() {
        prefix[idx] = prefix[idx - 1] * nums[idx - 1];
    }

    // Calculate the suffix product
    let mut suffix = vec![1; nums.len()];
    for idx in (0..nums.len() - 1).rev() {
        suffix[idx] = suffix[idx + 1] * nums[idx + 1];
    }

    // calculate the result
    let mut result = vec![0; nums.len()];
    for idx in 0..nums.len() {
        result[idx] = prefix[idx] * suffix[idx];
    }

    result
}
```

### Calculating the suffix in-place

We can calculate either the prefix or the suffix product in-place, thu solving
the problem with O(1) extra space (without counting the `result` vector):

```rust
pub fn product_except_self(mut nums: Vec<i32>) -> Vec<i32> {
    if nums.is_empty() {
        return nums;
    }

    // Calculate the prefix product
    let mut result = vec![1; nums.len()];
    for idx in 1..nums.len() {
        result[idx] = result[idx - 1] * nums[idx - 1];
    }

    // calculate the result by calculating the suffix in-place
    let mut suffix = 1;
    for idx in (0..nums.len() - 1).rev() {
        suffix *= nums[idx + 1];
        result[idx] *= suffix;
    }

    result
}
```