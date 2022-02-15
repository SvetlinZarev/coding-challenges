# [58. Length of Last Word](https://leetcode.com/problems/length-of-last-word/)

## Problem

Given a string `s` consisting of some words separated by some number of spaces,
return the length of the last word in the string.

A word is a maximal substring consisting of non-space characters only.

#### Constraints

* `1 <= s.length <= 10^4`
* `s` consists of only English letters and spaces ' '.
* There will be at least one word in `s`.

#### Examples

```text
Input: s = "Hello World"
Output: 5
Explanation: The last word is "World" with length 5.
```

```text
Input: s = "   fly me   to   the moon  "
Output: 4
Explanation: The last word is "moon" with length 4.
```

```text
Input: s = "luffy is still joyboy"
Output: 6
Explanation: The last word is "joyboy" with length 6.
```

## Solutions

### Writing it from scratch

```rust
pub fn length_of_last_word(s: String) -> i32 {
    let s = s.as_bytes();

    let mut word_end = s.len();
    while word_end > 0 && s[word_end - 1] == b' ' {
        word_end -= 1;
    }

    let mut word_start = word_end - 1;
    while word_start > 0 && s[word_start - 1] != b' ' {
        word_start -= 1;
    }

    (word_end - word_start) as i32
}
```

### Using the built-in iterators

```rust
pub fn length_of_last_word(s: String) -> i32 {
    s.split_whitespace()
        .rev()
        .next()
        .map(|s| s.len() as i32)
        .unwrap()
}
```