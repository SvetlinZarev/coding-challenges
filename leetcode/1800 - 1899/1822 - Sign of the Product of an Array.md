# [1822. Sign of the Product of an Array](https://leetcode.com/problems/sign-of-the-product-of-an-array/)

## Problem

### Description

There is a function `signFunc(x)` that returns:

* `1` if `x` is positive.
* `-1` if `x` is negative.
* `0` if `x` is equal to `0`.

You are given an integer array `nums`. Let product be the product of all values
in the array `nums`.

Return `signFunc(product)`.

### Constraints

* `1 <= nums.length <= 1000`
* `-100 <= nums[i] <= 100`

### Examples

```text
Input: nums = [-1,-2,-3,-4,3,2,1]
Output: 1
Explanation: The product of all values in the array is 144, and signFunc(144) = 1
```

```text
Input: nums = [1,5,0,2,-3]
Output: 0
Explanation: The product of all values in the array is 0, and signFunc(0) = 0
```

```text
Input: nums = [-1,1,-1,1,-1]
Output: -1
Explanation: The product of all values in the array is -1, and signFunc(-1) = -1
```

## Solutions

### With a loop

```rust
pub fn array_sign(nums: Vec<i32>) -> i32 {
    let mut sign = 1;
    for x in nums.into_iter() {
        sign *= x.signum();
        // there is no need to check the rest if we've encountered 0
        if sign == 0 {
            break;
        }
    }
    sign
}
```

### With iterators, without early termination

```rust
pub fn array_sign(nums: Vec<i32>) -> i32 {
    nums.into_iter().map(|x| x.signum()).product()
}
```

### With iterators + early termination

```rust
pub fn array_sign(nums: Vec<i32>) -> i32 {
    nums.into_iter()
        .map(|x| x.signum())
        .try_fold(1, |acc, val| match val {
            0 => None,
            x => Some(acc * x),
        })
        .unwrap_or(0)
}
```