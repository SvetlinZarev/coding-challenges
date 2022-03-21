# [67. Add Binary](https://leetcode.com/problems/add-binary/)

## Problem

### Description

Given two binary strings `a` and `b`, return their sum as a binary string.

### Constraints

* `1 <= a.length, b.length <= 10^4`
* `a` and `b` consist only of `0` or `1` characters.
* Each string does not contain leading zeros except for the zero itself.

### Examples

```text
Input: a = "11", b = "1"
Output: "100"
```

```text
Input: a = "1010", b = "1011"
Output: "10101"
```

## Solution

```rust
pub fn add_binary(a: String, b: String) -> String {
    let (shorter, mut longer) = if a.len() < b.len() {
        (a.as_bytes(), b.as_bytes().to_vec())
    } else {
        (b.as_bytes(), a.as_bytes().to_vec())
    };

    let slen = shorter.len();
    let llen = longer.len();

    let mut overflow = 0;
    for idx in 0..slen {
        let r_pos = llen - idx - 1;
        let s_pos = slen - idx - 1;

        let x = longer[r_pos] - b'0' + shorter[s_pos] - b'0' + overflow;
        overflow = x / 2;
        longer[r_pos] = x % 2 + b'0';
    }

    for idx in slen..llen {
        let r_pos = llen - idx - 1;
        let x = longer[r_pos] - b'0' + overflow;
        overflow = x / 2;
        longer[r_pos] = x % 2 + b'0';
    }

    if overflow != 0 {
        longer.insert(0, b'1');
    }

    String::from_utf8(longer).unwrap()
}
```

## Related Problems

* [66. Plus One](/leetcode/000%20-%20099/66%20-%20Plus%20One.md)
* [415. Add Strings](/leetcode/400%20-%20499/415%20-%20Add%20Strings.md)
* [989. Add to Array-Form of Integer](/leetcode/900%20-%20999/989%20-%20Add%20to%20Array-Form%20of%20Integer.md)