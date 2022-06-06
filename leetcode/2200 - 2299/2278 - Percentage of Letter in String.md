# [2278. Percentage of Letter in String](https://leetcode.com/problems/percentage-of-letter-in-string/)

## Problem

### Description

Given a string `s` and a character `letter`, return the percentage of characters
in `s` that equal `letter` rounded down to the nearest whole percent.

### Constraints

* `1 <= s.length <= 100`
* `s` consists of lowercase English letters.
* `letter` is a lowercase English letter.

### Examples

```text
Input: s = "foobar", letter = "o"
Output: 33
Explanation:
The percentage of characters in s that equal the letter 'o' is 2 / 6 * 100% = 33% when rounded down, so we return 33.
```

```text
Input: s = "jjjj", letter = "k"
Output: 0
Explanation:
The percentage of characters in s that equal the letter 'k' is 0%, so we return 0.
```

## Solutions

### With iterators

```rust
pub fn percentage_letter(s: String, letter: char) -> i32 {
    if s.is_empty() {
        return 0;
    }

    let total = s.chars().count();
    let occurrences = s.chars().filter(|&ch| ch == letter).count();

    (occurrences * 100 / total) as i32
}
```