# [848. Shifting Letters](https://leetcode.com/problems/shifting-letters/)

## Problem

You are given a string `s` of lowercase English letters and an integer
array `shifts` of the same length.

Call the `shift()` of a letter, the next letter in the alphabet, (wrapping
around so that `z` becomes `a`).

* For example, `shift('a') = 'b'`, `shift('t') = 'u'`, and `shift('z') = 'a'`.

Now for each `shifts[i] = x`, we want to shift the first `i + 1` letters of `s`
, `x` times.

Return the final string after all such shifts to `s` are applied.

#### Constraints

* `1 <= s.length <= 10^5`
* `s` consists of lowercase English letters.
* `shifts.length == s.length`
* `0 <= shifts[i] <= 10^9`

#### Examples

```text
Input: s = "abc", shifts = [3,5,9]
Output: "rpl"
Explanation: We start with "abc".
After shifting the first 1 letters of s by 3, we have "dbc".
After shifting the first 2 letters of s by 5, we have "igc".
After shifting the first 3 letters of s by 9, we have "rpl", the answer.
```

```text
Input: s = "aaa", shifts = [1,2,3]
Output: "gfd"
```

## Solution

```rust
const LETTERS: u8 = b'z' - b'a' + 1;
const OFFSET: u8 = b'a';

pub fn shifting_letters(mut s: String, shifts: Vec<i32>) -> String {
    assert_eq!(s.as_bytes().len(), shifts.len());

    let mut shift = 0;
    for (idx, v) in unsafe { s.as_bytes_mut().iter_mut().enumerate().rev() } {
        shift = (shift + shifts[idx]) % LETTERS as i32;
        *v = (*v - OFFSET + shift as u8) % LETTERS + OFFSET;
    }

    s
}
```