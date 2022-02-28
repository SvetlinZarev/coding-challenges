# [1790. Check if One String Swap Can Make Strings Equal](https://leetcode.com/problems/check-if-one-string-swap-can-make-strings-equal/)

## Problem

### Description

You are given two strings `s1` and `s2` of equal length. A string swap is an
operation where you choose two indices in a string (not necessarily different)
and swap the characters at these indices.

Return `true` if it is possible to make both strings equal by performing at most
one string swap on exactly one of the strings. Otherwise, return `false`.

### Constraints

* `1 <= s1.length, s2.length <= 100`
* `s1.length == s2.length`
* `s1` and `s2` consist of only lowercase English letters.

### Examples

```text
Input: s1 = "bank", s2 = "kanb"
Output: true
Explanation: For example, swap the first character with the last character of s2 to make "bank".
```

```text
Input: s1 = "attack", s2 = "defend"
Output: false
Explanation: It is impossible to make them equal with one string swap.
```

```text
Input: s1 = "kelb", s2 = "kelb"
Output: true
Explanation: The two strings are already equal, so no string swap operation is required.
```

## Solutions

```rust
pub fn are_almost_equal(s1: String, s2: String) -> bool {
    assert_eq!(s1.len(), s2.len());
    let s1 = s1.as_bytes();
    let s2 = s2.as_bytes();

    let mut diffs = [0, 0];
    let mut diff_idx = 0;

    for idx in 0..s1.len() {
        if s1[idx] != s2[idx] {
            if diff_idx >= diffs.len() {
                return false;
            }

            diffs[diff_idx] = idx;
            diff_idx += 1;
        }
    }

    if diff_idx != 0 && diff_idx != 2 {
        return false;
    }

    s1[diffs[0]] == s2[diffs[1]] && s1[diffs[1]] == s2[diffs[0]]
}
```