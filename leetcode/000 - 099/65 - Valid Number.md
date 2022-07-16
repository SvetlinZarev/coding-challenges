# [65. Valid Number](https://leetcode.com/problems/valid-number/)

## Problem

### Description

A valid number can be split up into these components (in order):

* A decimal number or an integer.
* (Optional) An `e` or `E`, followed by an integer.

A decimal number can be split up into these components (in order):

* (Optional) A sign character (either `+` or `-`).
* One of the following formats:
    * One or more digits, followed by a dot `.`.
    * One or more digits, followed by a dot `.`, followed by one or more digits.
    * A dot `.`, followed by one or more digits.

An integer can be split up into these components (in order):

* (Optional) A sign character (either `+` or `-`).
* One or more digits.

For example, all the following are valid numbers:

* `2`
* `0089`
* `-0.1`
* `+3.14`
* `4.`
* `-.9`
* `2e10`
* `-90E3`
* `3e+7`
* `+6e-1`
* `53.5e93`
* `-123.456e789`

while the following are not valid numbers:

* `abc`
* `1a`
* `1e`
* `e3`
* `99e2.5`
* `--6`
* `-+3`
* `95a54e53`

Given a string `s`, return `true` if `s` is a valid number.

### Constraints

* `1 <= s.length <= 20`
* `s` consists of only English letters (both uppercase and lowercase), digits (
  0-9), plus `+`, minus `-`, or dot `.`.

### Examples

```text
Input: s = "0"
Output: true
```

```text
Input: s = "e"
Output: false
```

```text
Input: s = "."
Output: false
```

## Solutions

### Building a Finite State Machine

```rust
#[derive(Copy, Clone, Eq, PartialEq)]
enum State {
    Initial,
    Invalid,
    Sign,
    Digit,
    LeadingDot,
    FracDot,
    FracDigit,
    Exponent,
    ExpSign,
    ExpDigit,
}

impl State {
    pub fn new() -> Self {
        State::Initial
    }

    pub fn is_terminal(self) -> bool {
        match self {
            State::Invalid => true,
            _ => false,
        }
    }

    pub fn is_valid_number(self) -> bool {
        match self {
            State::Digit => true,
            State::FracDot => true,
            State::FracDigit => true,
            State::ExpDigit => true,
            _ => false,
        }
    }

    pub fn next(self, ch: char) -> Self {
        match self {
            State::Initial => on_initial(ch),
            State::Sign => on_sign(ch),
            State::LeadingDot => on_leading_dot(ch),
            State::Digit => on_digit(ch),
            State::FracDot => on_frac_dot(ch),
            State::FracDigit => on_frac_digit(ch),
            State::Exponent => on_exponent(ch),
            State::ExpSign => on_exponent_sign(ch),
            State::ExpDigit => on_exponent_digit(ch),
            State::Invalid => State::Invalid,
        }
    }
}

fn on_initial(ch: char) -> State {
    match ch {
        '+' | '-' => State::Sign,
        '0'..='9' => State::Digit,
        '.' => State::LeadingDot,
        _ => State::Invalid,
    }
}

fn on_sign(ch: char) -> State {
    match ch {
        '0'..='9' => State::Digit,
        '.' => State::LeadingDot,
        _ => State::Invalid,
    }
}

fn on_leading_dot(ch: char) -> State {
    match ch {
        '0'..='9' => State::FracDigit,
        _ => State::Invalid,
    }
}

fn on_digit(ch: char) -> State {
    match ch {
        '0'..='9' => State::Digit,
        '.' => State::FracDot,
        'e' | 'E' => State::Exponent,
        _ => State::Invalid,
    }
}

fn on_frac_dot(ch: char) -> State {
    match ch {
        '0'..='9' => State::FracDigit,
        'e' | 'E' => State::Exponent,
        _ => State::Invalid,
    }
}

fn on_frac_digit(ch: char) -> State {
    match ch {
        '0'..='9' => State::FracDigit,
        'e' | 'E' => State::Exponent,
        _ => State::Invalid,
    }
}

fn on_exponent(ch: char) -> State {
    match ch {
        '0'..='9' => State::ExpDigit,
        '+' | '-' => State::ExpSign,
        _ => State::Invalid,
    }
}

fn on_exponent_sign(ch: char) -> State {
    match ch {
        '0'..='9' => State::ExpDigit,
        _ => State::Invalid,
    }
}

fn on_exponent_digit(ch: char) -> State {
    match ch {
        '0'..='9' => State::ExpDigit,
        _ => State::Invalid,
    }
}

// The entry point for LeetCode
pub fn is_number<S: AsRef<str>>(s: S) -> bool {
    let mut state = State::new();
    for ch in s.as_ref().chars() {
        state = state.next(ch);
        if state.is_terminal() {
            break;
        }
    }
    state.is_valid_number()
}
```
