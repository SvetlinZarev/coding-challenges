# [258. Add Digits](https://leetcode.com/problems/add-digits/)

## Problem

Given an integer `num`, repeatedly add all its digits until the result has only
one digit, and return it.

#### Constraints

* `  0 <= num <= 2^31 - 1`

#### Examples

```text
Input: num = 38
Output: 2
Explanation: The process is
38 --> 3 + 8 --> 11
11 --> 1 + 1 --> 2 
Since 2 has only one digit, return it.
```

```text
Input: num = 0
Output: 0
```

## Solutions

### MOD trick

```rust
pub fn add_digits(num: i32) -> i32 {
    if num == 0 {
        return 0;
    }

    match num % 9 {
        0 => 9,
        x => x,
    }
}
```

### With loops

```rust
pub fn add_digits(mut num: i32) -> i32 {
    let mut answer = num;

    while num > 9 {
        answer = 0;
        while num != 0 {
            answer += num % 10;
            num /= 10;
        }
        num = answer;
    }

    answer
}
```