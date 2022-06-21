# [205. Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/)

## Problem

### Description

Given two strings `s` and `t`, determine if they are isomorphic.

Two strings `s` and `t` are isomorphic if the characters in `s` can be replaced
to get `t`.

All occurrences of a character must be replaced with another character while
preserving the order of characters. No two characters may map to the same
character, but a character may map to itself.

### Constraints

* `1 <= s.length <= 5 * 10^4`
* `t.length == s.length`
* `s` and `t` consist of any valid ascii character.

### Examples

```text
Input: s = "egg", t = "add"
Output: true
```

```text
Input: s = "foo", t = "bar"
Output: false
```

```text
Input: s = "paper", t = "title"
Output: true
```

## Solutions

### Mapping characters to numbers

Because we are limited to the ASCII range, we can use a stack allocated array
instead of `HashMap`

```rust
pub fn is_isomorphic<S: AsRef<str>>(s: S, t: S) -> bool {
    let s = s.as_ref().as_bytes();
    let t = t.as_ref().as_bytes();
    if s.len() != t.len() {
        return false;
    }

    let x = remap(s);
    let y = remap(t);

    x == y
}

fn remap(input: &[u8]) -> Vec<u8> {
    let mut mapping = [0u8; 256];
    let mut counter = 0;
    let mut result = vec![0; input.len()];

    for idx in 0..input.len() {
        if mapping[input[idx] as usize] == 0 {
            counter += 1;
            mapping[input[idx] as usize] = counter;
        }

        result[idx] = mapping[input[idx] as usize];
    }

    result
}
```
