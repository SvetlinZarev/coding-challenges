# [8. String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/)

## Problem

Implement the `myAtoi(string s)` function, which converts a string to a 32-bit
signed integer (similar to C/C++'s `atoi` function).

The algorithm for `myAtoi(string s)` is as follows:

1. Read in and ignore any leading whitespace.
2. Check if the next character (if not already at the end of the string) is '-'
   or '+'. Read this character in if it is either. This determines if the final
   result is negative or positive respectively. Assume the result is positive if
   neither is present.
3. Read in next the characters until the next non-digit character or the end of
   the input is reached. The rest of the string is ignored.
4. Convert these digits into an integer (i.e. "123" -> 123, "0032" -> 32). If no
   digits were read, then the integer is 0. Change the sign as necessary (from
   step 2).
5. If the integer is out of the 32-bit signed integer range `[-2^31, 2^31 - 1]`,
   then clamp the integer so that it remains in the range. Specifically,
   integers less than `-2^31` should be clamped to `-2^31`, and integers greater
   than `2^31 - 1` should be clamped to `2^31 - 1`.
6. Return the integer as the final result.

**Note:**

* Only the space character is considered a whitespace character.
* Do not ignore any characters other than the leading whitespace or the rest of
  the string after the digits.

#### Constraints:

* `0 <= s.length <= 200`
* `s` consists of English letters (lower-case and upper-case), digits (0-9), ' '
  , '+', '-', and '.'.

## Solution

```rust
#[derive(Debug, Copy, Clone)]
enum Sign {
    Positive,
    Negative,
}

#[derive(Debug, Copy, Clone)]
enum State {
    WhiteSpace,
    Digits,
}

pub fn my_atoi(s: String) -> i32 {
    let mut sign = Sign::Positive;
    let mut state = State::WhiteSpace;

    let mut number = 0i64;
    for ch in s.as_bytes().iter().copied() {
        match state {
            State::WhiteSpace => match ch {
                b'+' => {
                    state = State::Digits;
                }

                b'-' => {
                    state = State::Digits;
                    sign = Sign::Negative;
                }

                a @ b'0'..=b'9' => {
                    state = State::Digits;
                    number = number * 10 + (a - b'0') as i64;
                }

                b' ' => {
                    //skip;
                }

                _ => {
                    break;
                }
            },

            State::Digits => match ch {
                a @ b'0'..=b'9' => {
                    state = State::Digits;
                    number = number * 10 + (a - b'0') as i64;
                }

                _ => {
                    break;
                }
            },
        }

        if number >= i32::MAX as i64 + 1 {
            break;
        }
    }

    number = match sign {
        Sign::Positive => number,
        Sign::Negative => -number,
    };

    number = if number < i32::MIN as i64 {
        i32::MIN as i64
    } else if number > i32::MAX as i64 {
        i32::MAX as i64
    } else {
        number
    };

    number as i32
}
```