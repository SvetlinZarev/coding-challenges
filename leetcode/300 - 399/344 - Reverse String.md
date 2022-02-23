# [344. Reverse String](https://leetcode.com/problems/reverse-string/)

## Problem

Write a function that reverses a string. The input string is given as an array
of characters `s`.

You must do this by modifying the input array in-place with O(1) extra memory.

#### Constraints

* `1 <= s.length <= 10^5`
* `s[i]` is a printable ascii character.

#### Examples

```text
Input: s = ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

```text
Input: s = ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

## Solution

```rust
pub fn reverse_string(s: &mut Vec<char>) {
    if s.is_empty() {
        return;
    }

    let mut a = 0;
    let mut b = s.len() - 1;
    while a < b {
        s.swap(a, b);
        a += 1;
        b -= 1;
    }
}
```