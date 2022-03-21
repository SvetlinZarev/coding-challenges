# [989. Add to Array-Form of Integer](https://leetcode.com/problems/add-to-array-form-of-integer/)

## Problem

### Description

The array-form of an integer `num` is an array representing its digits in left
to right order.

* For example, for `num = 1321`, the array form is `[1,3,2,1]`.

Given `num`, the array-form of an integer, and an integer `k`, return the
array-form of the integer `num + k`.

### Constraints

* `1 <= num.length <= 10^4`
* `0 <= num[i] <= 9`
* `num` does not contain any leading zeros except for the zero itself.
* `1 <= k <= 10^4`

### Examples

```text
Input: num = [1,2,0,0], k = 34
Output: [1,2,3,4]
Explanation: 1200 + 34 = 1234
```

```text
Input: num = [2,7,4], k = 181
Output: [4,5,5]
Explanation: 274 + 181 = 455
```

```text
Input: num = [2,1,5], k = 806
Output: [1,0,2,1]
Explanation: 215 + 806 = 1021
```

## Solutions

### Modify the number in-place #1

```rust
pub fn add_to_array_form(mut num: Vec<i32>, k: i32) -> Vec<i32> {
    let mut remaining = k;
    let mut overflow = 0;

    for value in num.iter_mut().rev() {
        let x = remaining % 10 + overflow;
        remaining /= 10;

        *value += x;
        overflow = *value / 10;
        *value = *value % 10;
    }

    remaining += overflow;
    while remaining > 0 {
        // Inserting the values in from is not efficient, because it will make all the
        // other elements shift to the right on each insert
        num.insert(0, remaining % 10);
        remaining /= 10;
    }

    num
}
```

### Modify the number in-place #2

In order to avoid the inefficiencies when inserting at the front of the array,
we reverse it once at the beginning, and then one more time at the end. Thus, we
can do `.push()` instead of `.insert()`:

```rust
pub fn add_to_array_form(mut num: Vec<i32>, k: i32) -> Vec<i32> {
    let mut remaining = k;
    let mut overflow = 0;

    num.reverse();
    for value in num.iter_mut() {
        let x = remaining % 10 + overflow;
        remaining /= 10;

        *value += x;
        overflow = *value / 10;
        *value = *value % 10;
    }

    remaining += overflow;
    while remaining > 0 {
        // Inserting the values in from is not efficient, because it will make all the
        // other elements shift to the right on each insert
        num.push(remaining % 10);
        remaining /= 10;
    }

    num.reverse();
    num
}
```

## Related Problems

* [66. Plus One](/leetcode/000%20-%20099/66%20-%20Plus%20One.md)
* [67. Add Binary](/leetcode/000%20-%20099/67%20-%20Add%20Binary.md)
* [415. Add Strings](/leetcode/400%20-%20499/415%20-%20Add%20Strings.md)