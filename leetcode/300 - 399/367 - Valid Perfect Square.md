# [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)

## Problem

### Description

Given a positive integer `num`, write a function which returns `True` if `num`
is a perfect square else `False`. **Do not use** any built-in library function
such as `sqrt()`.

### Constraints

* `1 <= num <= 2^31 - 1`

### Examples

```text
Input: num = 16
Output: true
```

```text
Input: num = 14
Output: false
```

## Solutions

### Binary Search

```rust
use std::cmp::Ordering;

pub fn is_perfect_square(num: i32) -> bool {
    let mut lo = 0;
    let mut hi = num;

    while lo <= hi {
        let mid = (hi - lo) / 2 + lo;

        // if `mid` is too large, it's possible to overflow, that's why we use `.checked_mul()`
        match mid.checked_mul(mid) {
            None => hi = mid - 1,
            Some(x) => match x.cmp(&num) {
                Ordering::Equal => return true,
                Ordering::Less => lo = mid + 1,
                Ordering::Greater => hi = mid - 1,
            },
        }
    }

    false
}
```

## Related Problems

* [69. Sqrt(x)](/leetcode/000%20-%20099/69%20-%20Sqrt(x).md)
* [633. Sum of Square Numbers](/leetcode/600%20-%20699/633%20-%20Sum%20of%20Square%20Numbers.md)