# [13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/)

## Problem

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`
, `C`, `D` and `M`.

```text
|Symbol|       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, 2 is written as `II` in Roman numeral, just two one's added
together. 12 is written as `XII`, which is simply `X + II`. The number 27 is
written as `XXVII`, which is `XX + V + II`.

Roman numerals are usually written largest to smallest from left to right.
However, the numeral for four is not `IIII`. Instead, the number four is written
as `IV`. Because the one is before the five we subtract it making four. The same
principle applies to the number nine, which is written as `IX`. There are six
instances where subtraction is used:

* I can be placed before V (5) and X (10) to make 4 and 9.
* X can be placed before L (50) and C (100) to make 40 and 90.
* C can be placed before D (500) and M (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer.

## Solution

```rust

pub fn roman_to_int(s: String) -> i32 {
    let mut map = [0i32; (b'Z' - b'A' + 1) as usize];
    map[(b'I' - b'A') as usize] = 1;
    map[(b'V' - b'A') as usize] = 5;
    map[(b'X' - b'A') as usize] = 10;
    map[(b'L' - b'A') as usize] = 50;
    map[(b'C' - b'A') as usize] = 100;
    map[(b'D' - b'A') as usize] = 500;
    map[(b'M' - b'A') as usize] = 1000;

    let mut number = map[(s.as_bytes()[s.len() - 1] - b'A') as usize];
    for ch in s.as_bytes().windows(2).rev() {
        let a = map[(ch[0] - b'A') as usize];
        let b = map[(ch[1] - b'A') as usize];
        if a >= b {
            number += a;
        } else {
            number -= a;
        }
    }

    number
}
```