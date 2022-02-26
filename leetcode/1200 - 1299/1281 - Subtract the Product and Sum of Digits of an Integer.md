# [1281. Subtract the Product and Sum of Digits of an Integer](https://leetcode.com/problems/subtract-the-product-and-sum-of-digits-of-an-integer/)

## Problem

### Description

Given an integer number `n`, return the difference between the product of its
digits and the sum of its digits.

### Constraints

* `1 <= n <= 10^5`

### Examples

```text
Input: n = 234
Output: 15 
Explanation: 
Product of digits = 2 * 3 * 4 = 24 
Sum of digits = 2 + 3 + 4 = 9 
Result = 24 - 9 = 15
```

```text
Input: n = 4421
Output: 21
Explanation: 
Product of digits = 4 * 4 * 2 * 1 = 32 
Sum of digits = 4 + 4 + 2 + 1 = 11 
Result = 32 - 11 = 21
```

## Solution

```rust
pub fn subtract_product_and_sum(mut n: i32) -> i32 {
    let mut sum = 0;
    let mut product = (n > 0) as i32;

    while n > 0 {
        let x = n % 10;
        n = n / 10;

        sum += x;
        product *= x;
    }

    product - sum
}
```