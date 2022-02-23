# [1694. Reformat Phone Number](https://leetcode.com/problems/reformat-phone-number/)

## Problem

You are given a phone number as a string number. number consists of digits,
spaces ' ', and/or dashes '-'.

You would like to reformat the phone number in a certain manner. Firstly, remove
all spaces and dashes. Then, group the digits from left to right into blocks of
length 3 until there are 4 or fewer digits. The final digits are then grouped as
follows:

* 2 digits: A single block of length 2.
* 3 digits: A single block of length 3.
* 4 digits: Two blocks of length 2 each.

The blocks are then joined by dashes. Notice that the reformatting process
should never produce any blocks of length 1 and produce at most two blocks of
length 2.

Return the phone number after formatting.

#### Examples

```text
Input: number = "123 4-5678"
Output: "123-456-78"
Explanation: The digits are "12345678".
Step 1: The 1st block is "123".
Step 2: The 2nd block is "456".
```

## Solution

```rust
pub fn reformat_number(number: String) -> String {
    let mut result = String::new();

    for ch in number.chars().filter(|ch| ch.is_ascii_digit()) {
        result.push(ch);

        if result.len() % 4 == 3 {
            result.push('-')
        }
    }

    if result.ends_with('-') {
        result.truncate(result.len() - 1);
    }

    let ending_digits = result
        .chars()
        .rev()
        .take(2)
        .filter(|ch| ch.is_ascii_digit())
        .count();

    if ending_digits == 1 && result.len() > 2 {
        result.remove(result.len() - 2);
        result.insert(result.len() - 2, '-');
    }

    result
}
```