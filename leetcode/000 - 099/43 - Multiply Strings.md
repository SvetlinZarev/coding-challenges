# [43. Multiply Strings](https://leetcode.com/problems/multiply-strings/)

## Problem

Given two non-negative integers num1 and num2 represented as strings, 
return the product of `num1` and `num2`, also represented as a string.

**Note:** You must not use any built-in BigInteger library or convert 
the inputs to integer directly.

#### Constraints

* `1 <= num1.length, num2.length <= 200`
* `num1` and `num2` consist of digits only.
* Both `num1` and `num2` do not contain any leading zero, except the 
  number `0` itself.

#### Examples

```text
Input: num1 = "2", num2 = "3"
Output: "6"
```

```text
Input: num1 = "123", num2 = "456"
Output: "56088"
```

## Solution

This solution implements the naive method, as done by a human:

```text
    11 x 11
    -------
    11  // t1 (in the code below)
  +110  // t2
   ---
   121  // r
```

```rust
pub fn multiply(num1: String, num2: String) -> String {
    // if any of the strings is `"0"`, thene there is no point in doing anything
    if num1 == "0" {
        return num1;
    }

    if num2 == "0" {
        return num2;
    }

    // reusable storage for the intermediate computations
    let mut r = Vec::with_capacity(num1.len().max(num2.len()) + 16); // the sum of the two intermediate numbers
    let mut t1 = Vec::with_capacity(num1.len().max(num2.len()) + 16); // the first intermediate
    let mut t2 = Vec::with_capacity(num1.len().max(num2.len()) + 16); // the second intermediate

    let n1 = num1.as_bytes();
    let n2 = num2.as_bytes();

    // the original strings are in "big endian" order, but we need to process
    // the least signifacant digits first, so traverse them in reverse order
    let mut i = n1.len();
    while i > 0 {
        i -= 1;
        let x = to_num(n1[i]);

        // append the required trailing zeroes (see the 11x11 diagram above)
        for _ in 0..n1.len() - i - 1 {
            t1.push(0);
        }

        let mut carry = 0;
        let mut j = n2.len();

        while j > 0 {
            j -= 1;
            let y = to_num(n2[j]);

            let m = x * y + carry;
            let digit = m % 10;
            carry = m / 10;

            t1.push(digit);
        }

        if carry != 0 {
            t1.push(carry);
        }

        std::mem::swap(&mut t2, &mut r);
        sum(&t1, &t2, &mut r);
        t1.clear();
    }

    // we've been working in "little endian" mode - i.e. the least significant digits were first,
    // so we need to reorder the digits in "big endian" format - i.e. the most significant difgit
    // first
    r.reverse();
    
    // convert the digits to ASCII
    r.iter_mut().for_each(|ch| *ch = to_char(*ch));
    
    // SAFETY: the algorithm guarantees that all bytes contain only
    // values in the range [b'0'; b'9']
    unsafe { String::from_utf8_unchecked(r) }
}

fn to_num(ch: u8) -> u8 {
    debug_assert!(ch <= b'9');

    ch as u8 - b'0'
}

fn to_char(digit: u8) -> u8 {
    debug_assert!(digit <= 9);

    digit as u8 + b'0'
}

// the arguments should contain the digits in reverse order
// the result will be in reverse order as well
fn sum(a: &[u8], b: &[u8], dst: &mut Vec<u8>) {
    dst.clear();

    let mut i = 0;
    let mut j = 0;

    let mut carry = 0;
    while i < a.len() && j < b.len() {
        let x = a[i];
        let y = b[j];

        let m = x + y + carry;
        let digit = m % 10;
        carry = m / 10;

        dst.push(digit);

        i += 1;
        j += 1;
    }

    // process the remaining digits from the longer string
    let mut k = i;
    let mut n = a;
    if i == a.len() {
        k = j;
        n = b;
    }

    while k < n.len() {
        let x = n[k];

        let m = x + carry;
        let digit = m % 10;
        carry = m / 10;

        dst.push(digit);
        k += 1;
    }

    if carry != 0 {
        dst.push(carry)
    }
}
```
