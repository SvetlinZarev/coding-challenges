# [415. Add Strings](https://leetcode.com/problems/add-strings/)

## Problem

### Description

Given two non-negative integers, `num1` and `num2` represented as string, return
the sum of `num1` and `num2` as a string.

You must solve the problem without using any built-in library for handling large
integers (such as `BigInteger`). You must also not convert the inputs to
integers directly.

### Constraints

* `1 <= num1.length, num2.length <= 10^4`
* `num1` and `num2` consist of only digits.
* `num1` and `num2` don't have any leading zeros except for the zero itself.

### Examples

```text
Input: num1 = "11", num2 = "123"
Output: "134"
```

```text
Input: num1 = "456", num2 = "77"
Output: "533"
```

```text
Input: num1 = "0", num2 = "0"
Output: "0"
```

## Solutions

```rust
pub fn add_strings(num1: String, num2: String) -> String {
    assert!(num1.len() > 0);
    assert!(num2.len() > 0);

    let mut a = num1.chars().rev();
    let mut b = num2.chars().rev();

    let mut answer = vec![];
    let mut overflow = 0;

    loop {
        let x = a.next();
        let y = b.next();

        if x.is_none() && y.is_none() {
            if overflow != 0 {
                answer.push(char::from_digit(overflow, 10).unwrap());
            }

            break;
        }

        let x = x.map(|val| val.to_digit(10).unwrap()).unwrap_or(0);
        let y = y.map(|val| val.to_digit(10).unwrap()).unwrap_or(0);

        let sum = x + y + overflow;
        let digit = sum % 10;
        overflow = sum / 10;

        answer.push(char::from_digit(digit, 10).unwrap());
    }

    answer.into_iter().rev().collect()
}
```

## Related Problems

* [67. Add Binary](/leetcode/000%20-%20099/67%20-%20Add%20Binary.md)
* [989. Add to Array-Form of Integer](/leetcode/900%20-%20999/989%20-%20Add%20to%20Array-Form%20of%20Integer.md)
