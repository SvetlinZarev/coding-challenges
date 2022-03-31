# [557. Reverse Words in a String III](https://leetcode.com/problems/reverse-words-in-a-string-iii/)

## Problem

### Description

Given a string `s`, reverse the order of characters in each word within a
sentence while still preserving whitespace and initial word order.

### Constraints

* `1 <= s.length <= 5 * 10^4`
* `s` contains printable ASCII characters.
* `s` does not contain any leading or trailing spaces.
* All the words in s are separated by a single space.
* There is at least one word in s.

### Examples

```text
Input: s = "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"
```

## Solutions

### Using iterators with

```rust
pub fn reverse_words(string: String) -> String {
    let mut answer = String::with_capacity(string.len());

    string.split_ascii_whitespace().for_each(|s| {
        if !answer.is_empty() {
            answer.push(' ');
        }
        answer.extend(s.chars().rev());
    });

    answer
}
```

### In-place using iterators

This is safe, because we are working only with ASCII characters which are always
1 byte wide

```rust
pub fn reverse_words(mut string: String) -> String {
    let s = unsafe { string.as_bytes_mut() };

    s.split_mut(|x| x.is_ascii_whitespace())
        .for_each(|x| x.reverse());

    string
}
```

### In-place using loops

This is safe, because we are working only with ASCII characters which are always
1 byte wide

```rust
pub fn reverse_words(mut string: String) -> String {
    let s = unsafe { string.as_bytes_mut() };

    let mut a = 0;
    for idx in 0..s.len() {
        if s[idx] == b' ' || idx == s.len() - 1 {
            let mut b = idx;
            if idx != s.len() - 1 {
                b -= 1;
            }

            while a < b {
                s.swap(a, b);
                a += 1;
                b -= 1;
            }
            a = idx + 1;
        }
    }

    string
}
```