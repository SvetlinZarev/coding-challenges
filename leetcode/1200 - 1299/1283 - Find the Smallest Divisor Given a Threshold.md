# [1283. Find the Smallest Divisor Given a Threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/)

## Problem

### Description

Given an array of integers `nums` and an integer `threshold`, we will choose a
positive integer `divisor`, divide all the array by it, and sum the division's
result. Find the **smallest** `divisor` such that the result mentioned above is
less than or equal to `threshold`.

Each result of the division is rounded to the nearest integer greater than or
equal to that element. (For example: `7/3 = 3` and `10/2 = 5`).

The test cases are generated so that there will be an answer.

### Constraints

* `1 <= nums.length <= 5 * 10^4`
* `1 <= nums[i] <= 10^6`
* `nums.length <= threshold <= 10^6`

### Examples

```text
Input: nums = [1,2,5,9], threshold = 6
Output: 5
Explanation: We can get a sum to 17 (1+2+5+9) if the divisor is 1. 
If the divisor is 4 we can get a sum of 7 (1+1+2+3) and if the divisor is 5 the sum will be 5 (1+1+1+2). 
```

```text
Input: nums = [44,22,33,11,1], threshold = 5
Output: 44
```

## Solutions

### Binary Search

```rust
pub fn smallest_divisor(nums: Vec<i32>, threshold: i32) -> i32 {
    let mut lo = 1;
    let mut hi = nums.iter().copied().max().unwrap();

    while lo < hi {
        let mid = lo + (hi - lo) / 2;
        let sum = sum(&nums, mid);

        if sum <= threshold {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    hi
}

fn sum(array: &[i32], divisor: i32) -> i32 {
    array.iter().copied().map(|x| ceil(x, divisor)).sum()
}

fn ceil(a: i32, b: i32) -> i32 {
    a / b + (a % b != 0) as i32
}
```

### Related Problems

* [875. Koko Eating Bananas](/leetcode/800%20-%20899/875%20-%20Koko%20Eating%20Bananas.md)
* [1870. Minimum Speed to Arrive on Time](/leetcode/1800%20-%201899/1870%20-%20Minimum%20Speed%20to%20Arrive%20on%20Time.md)