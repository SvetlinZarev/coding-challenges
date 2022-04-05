# [744. Find Smallest Letter Greater Than Target](https://leetcode.com/problems/find-smallest-letter-greater-than-target/)

## Problem

### Description

Given a characters array `letters` that is sorted in non-decreasing order and a
character `target`, return the smallest character in the array that is larger
than `target`.

**Note** that the letters wrap around.

* For example, if `target == 'z'` and `letters == ['a', 'b']`, the answer is `a`

### Constraints

* `2 <= letters.length <= 10^4`
* `letters[i]` is a lowercase English letter.
* `letters` is sorted in non-decreasing order.
* `letters` contains at least two different characters.
* `target` is a lowercase English letter.

### Examples

```text
Input: letters = ["c","f","j"], target = "a"
Output: "c"
```

```text
Input: letters = ["c","f","j"], target = "c"
Output: "f"
```

```text
Input: letters = ["c","f","j"], target = "j"
Output: "c"
```

```text
Input: letters = ["c","f","j"], target = "d"
Output: "f"
```

## Solutions

### Binary Search

```rust
pub fn next_greatest_letter(letters: Vec<char>, target: char) -> char {
    let mut lo = 0;
    let mut hi = letters.len();

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;
        if letters[mid] <= target {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }

    // because of the wrap-around (see the third example)
    if lo == letters.len() {
        lo = 0;
    }

    letters[lo]
}
```