# [1009. Complement of Base 10 Integer](https://leetcode.com/problems/complement-of-base-10-integer/)

## Problem

The complement of an integer is the integer you get when you flip all the 0's to 1's and all the 1's to 0's in its
binary representation. For example, The integer `5` is `101` in binary and its complement is `010` which is the
integer `2`.

Given an integer `n`, return its complement.

#### Constraints

* `0 <= n < 10^9`

#### Examples

```text
Input: n = 5
Output: 2
Explanation: 5 is "101" in binary, with complement "010" in binary, which is 2 in base-10.
```

```text
Input: n = 7
Output: 0
Explanation: 7 is "111" in binary, with complement "000" in binary, which is 0 in base-10.
```

```text
Input: n = 10
Output: 5
Explanation: 10 is "1010" in binary, with complement "0101" in binary, which is 5 in base-10.
```

## Solution

```rust
pub fn bitwise_complement(n: i32) -> i32 {
    if n == 0 {
        return 1;
    }

    (n ^ i32::MAX) & (i32::MAX >> n.leading_zeros())
}
```

## Related Problems

* [476. Number Complement](/400%20-%20499/476%20-%20Number%20Complement.md)