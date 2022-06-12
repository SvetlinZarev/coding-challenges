# [2259. Remove Digit From Number to Maximize Result](https://leetcode.com/problems/remove-digit-from-number-to-maximize-result/)

## Problem

### Description

You are given a string `number` representing a positive integer and a character
`digit`.

Return the resulting string after removing exactly one occurrence of `digit`
from `number` such that the value of the resulting string in decimal form is
maximized. The test cases are generated such that `digit` occurs at least once
in `number`.

### Constraints

* `2 <= number.length <= 100`
* `number` consists of digits from `1` to `9`.
* `digit` is a digit from `1` to `9`.
* `digit` occurs at least once in `number`.

### Examples

#### Example 1

```text
Input: number = "123", digit = "3"
Output: "12"
```

> Explanation: There is only one '3' in "123". After removing '3', the result
> is "12".

#### Example 2

```text
Input: number = "1231", digit = "1"
Output: "231"
```

> Explanation: We can remove the first '1' to get "231" or remove the second '1'
> to get "123". Since 231 > 123, we return "231".

#### Example 3

```text
Input: number = "551", digit = "5"
Output: "51"
```

> Explanation: We can remove either the first or second '5' from "551".

## Solutions

### Looking for the first occurrence of digit that is smaller than the next digit

```rust
pub fn remove_digit<S: Into<String>>(number: S, digit: char) -> String {
    assert!(digit.is_ascii_digit());

    let digit = digit as u8;
    let number = number.into();
    let mut bytes = number.into_bytes();

    let mut to_remove = 0;
    for idx in 0..bytes.len() {
        if bytes[idx] != digit {
            continue;
        }

        to_remove = idx;
        if idx < bytes.len() - 1 && bytes[idx] < bytes[idx + 1] {
            break;
        }
    }

    // make sure that it occurs at least once
    assert_eq!(digit, bytes[to_remove]);

    bytes.remove(to_remove);
    String::from_utf8(bytes).unwrap()
}
```
