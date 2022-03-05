# [1309. Decrypt String from Alphabet to Integer Mapping](https://leetcode.com/problems/decrypt-string-from-alphabet-to-integer-mapping/)

## Problem

### Description

You are given a string `s` formed by digits and `#`. We want to map `s` to
English lowercase characters as follows:

* Characters (`a` to `i`) are represented by (`1` to `9`) respectively.
* Characters (`j` to `z`) are represented by (`10#` to `26#`) respectively.

Return the string formed after mapping.

The test cases are generated so that a unique mapping will always exist.

### Constraints

* `1 <= s.length <= 1000`
* `s` consists of digits and the `#` letter.
* `s` will be a valid string such that mapping is always possible.

### Examples

```text
Input: s = "10#11#12"
Output: "jkab"
Explanation: "j" -> "10#" , "k" -> "11#" , "a" -> "1" , "b" -> "2".
```

```text
Input: s = "1326#"
Output: "acz"
```

## Solutions

```rust
pub fn freq_alphabets(s: String) -> String {
    let s = s.as_bytes();
    let mut d = vec![];

    let mut idx = s.len();
    while idx > 0 {
        idx -= 1;

        let val = if s[idx] == b'#' {
            idx -= 2;
            (s[idx] - b'0') * 10 + (s[idx + 1] - b'0')
        } else {
            s[idx] - b'0'
        };

        d.push(val - 1 + b'a'); // minus one, because "a" == 1, not 0
    }
    d.reverse();

    String::from_utf8(d).unwrap()
}
```