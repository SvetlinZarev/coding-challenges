# [12. Integer to Roman](https://leetcode.com/problems/integer-to-roman/)

## Problem

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`
, `C`, `D` and `M`.

```text
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, `2` is written as `II` in Roman numeral, just two ones added
together. `12` is written as `XII`, which is simply `X + II`. The number `27` is
written as `XXVII`, which is `XX + V + II`.

Roman numerals are usually written largest to smallest from left to right.
However, the numeral for four is not `IIII`. Instead, the number four is written
as `IV`. Because the one is before the five we subtract it making four. The same
principle applies to the number nine, which is written as `IX`. There are six
instances where subtraction is used:

* `I` can be placed before `V` (5) and `X` (10) to make 4 and 9.
* `X` can be placed before `L` (50) and `C` (100) to make 40 and 90.
* `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

Given an integer, convert it to a roman numeral.

#### Constraints

* `  1 <= num <= 3999`

#### Examples

```text
Input: num = 3
Output: "III"
```

```text
Input: num = 4
Output: "IV"
```

```text
Input: num = 9
Output: "IX"
```

```text
Input: num = 58
Output: "LVIII"
Explanation: L = 50, V = 5, III = 3.
```

```text
Input: num = 1994
Output: "MCMXCIV"
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

## Solution

### Naive solution

```rust
pub fn int_to_roman(mut num: i32) -> String {
    assert!(num > 0 && num <= 3999);

    let mut roman = String::new();

    while num >= 1000 {
        roman.push('M');
        num -= 1000;
    }

    if num >= 900 {
        roman.push_str("CM");
        num -= 900;
    }

    if num >= 500 {
        roman.push('D');
        num -= 500;
    }

    if num >= 400 {
        roman.push_str("CD");
        num -= 400;
    }

    while num >= 100 {
        roman.push('C');
        num -= 100;
    }

    if num >= 90 {
        roman.push_str("XC");
        num -= 90;
    }

    if num >= 50 {
        roman.push('L');
        num -= 50;
    }

    if num >= 40 {
        roman.push_str("XL");
        num -= 40;
    }

    while num >= 10 {
        roman.push('X');
        num -= 10;
    }

    if num == 9 {
        roman.push_str("IX");
        num -= 9;
    }

    if num >= 5 {
        roman.push('V');
        num -= 5;
        while num > 0 {
            roman.push('I');
            num -= 1;
        }
    }

    if num == 4 {
        roman.push_str("IV");
        num -= 4;
    }

    while num > 0 {
        roman.push('I');
        num -= 1;
    }

    roman
}
```

### Map based solution

```rust
const M: [&str; 4] = ["", "M", "MM", "MMM"];
const C: [&str; 10] = ["", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"];
const X: [&str; 10] = ["", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"];
const I: [&str; 10] = ["", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"];

pub fn int_to_roman(num: i32) -> String {
    assert!(num > 0 && num <= 3999);
    let n = num as usize;

    let mut roman = String::new();

    roman.push_str(M[(n / 1000)]);
    roman.push_str(C[((n % 1000) / 100)]);
    roman.push_str(X[((n % 100) / 10)]);
    roman.push_str(I[n % 10]);

    roman
}
```

### A more general solution

```rust
const SYM: [&str; 13] = [
    "M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I",
];

const VAL: [usize; 13] = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1];

pub fn int_to_roman(num: i32) -> String {
    assert!(num > 0 && num <= 3999);
    let mut n = num as usize;

    let mut roman = String::new();
    for (idx, v) in VAL.iter().copied().enumerate() {
        while n >= v {
            n -= v;
            roman.push_str(SYM[idx]);
        }
    }
    roman
}
```