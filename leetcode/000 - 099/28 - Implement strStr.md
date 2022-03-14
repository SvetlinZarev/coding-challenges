# [28. Implement strStr()](https://leetcode.com/problems/implement-strstr/)

## Problem

### Description

Implement `strStr()`.

Return the index of the first occurrence of `needle` in `haystack`, or `-1`
if `needle` is not part of `haystack`.

Clarification:

What should we return when `needle` is an empty string? This is a great question
to ask during an interview.

For the purpose of this problem, we will return `0` when `needle` is an empty
string. This is consistent to C's `strstr()` and Java's `indexOf()`.

### Constraints

* `0 <= haystack.length, needle.length <= 5 * 10^4`
* `haystack` and `needle` consist of only lower-case English characters.

### Examples

```text
Input: haystack = "hello", needle = "ll"
Output: 2
```

```text
Input: haystack = "aaaaa", needle = "bba"
Output: -1
```

```text
Input: haystack = "", needle = ""
Output: 0
```

## Solutions

### Naive implementation with nested loops

```rust
pub fn str_str(haystack: String, needle: String) -> i32 {
    let haystack = haystack.as_bytes();
    let needle = needle.as_bytes();

    if needle.is_empty() {
        return 0;
    }
    if haystack.len() < needle.len() {
        return -1;
    }

    'next: for idx in 0..haystack.len() - needle.len() + 1 {
        for pos in 0..needle.len() {
            if haystack[idx + pos] != needle[pos] {
                continue 'next;
            }
        }
        return idx as i32;
    }

    -1
}
```

### Naive implementation with loop

```rust
pub fn str_str(haystack: String, needle: String) -> i32 {
    let haystack = haystack.as_bytes();
    let needle = needle.as_bytes();

    if needle.is_empty() {
        return 0;
    }
    if haystack.len() < needle.len() {
        return -1;
    }

    for idx in 0..haystack.len() - needle.len() + 1 {
        if &haystack[idx..idx + needle.len()] == needle {
            return idx as i32;
        }
    }

    -1
}
```

### Naive implementation with iterators

```rust
pub fn str_str(haystack: String, needle: String) -> i32 {
    let haystack = haystack.as_bytes();
    let needle = needle.as_bytes();

    if needle.is_empty() {
        return 0;
    }
    if haystack.len() < needle.len() {
        return -1;
    }

    haystack
        .windows(needle.len())
        .position(|w| w == needle)
        .map_or(-1, |p| p as i32)
}
```

Although all 3 implementations should do the same thing, the one using iterators
is significantly faster, followed by the single-loop implementation. The nested
loops implementation is more than 25 times slower.