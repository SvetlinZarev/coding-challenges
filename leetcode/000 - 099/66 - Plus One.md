# [66. Plus One](https://leetcode.com/problems/plus-one/)

## Problem

### Description

You are given a large integer represented as an integer array `digits`, where
each `digits[i]` is the `i`th digit of the integer. The digits are ordered from
most significant to least significant in left-to-right order. The large integer
does not contain any leading `0`'s.

Increment the large integer by one and return the resulting array of digits.

### Constraints

* `1 <= digits.length <= 100`
* `0 <= digits[i] <= 9`
* `digits` does not contain any leading `0`'s.

### Examples

```text
Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
Incrementing by one gives 123 + 1 = 124.
Thus, the result should be [1,2,4].
```

```text
Input: digits = [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
Incrementing by one gives 4321 + 1 = 4322.
Thus, the result should be [4,3,2,2].
```

```text
Input: digits = [9]
Output: [1,0]
Explanation: The array represents the integer 9.
Incrementing by one gives 9 + 1 = 10.
Thus, the result should be [1,0].
```

## Solution

```rust
pub fn plus_one(mut digits: Vec<i32>) -> Vec<i32> {
    let mut overflow = 1;

    for d in digits.iter_mut().rev() {
        let x = *d + overflow;
        overflow = x / 10;
        *d = x % 10;

        if overflow == 0 {
            break;
        }
    }

    if overflow != 0 {
        digits.insert(0, overflow);
    }

    digits
}
```

## Related Problems

* [67. Add Binary](/leetcode/000%20-%20099/67%20-%20Add%20Binary.md)
* [415. Add Strings](/leetcode/400%20-%20499/415%20-%20Add%20Strings.md)
* [989. Add to Array-Form of Integer](/leetcode/900%20-%20999/989%20-%20Add%20to%20Array-Form%20of%20Integer.md)