# [409. Longest Palindrome](https://leetcode.com/problems/longest-palindrome/)

## Problem

### Description

Given a string `s` which consists of lowercase or uppercase letters, return the
length of the longest palindrome that can be built with those letters.

Letters are **case sensitive**, for example, `Aa` is not considered a palindrome
here.

### Constraints

* `1 <= s.length <= 2000`
* `s` consists of lowercase and/or uppercase English letters only.

### Examples

```text
Input: s = "abccccdd"
Output: 7
Explanation:
One longest palindrome that can be built is "dccaccd", whose length is 7.
```

```text
Input: s = "a"
Output: 1
```

```text
Input: s = "bb"
Output: 2
```

## Solutions

### Counting paired characters

A palindrome can be made up of either even, or odd number of characters.

* If it's made up of even number of characters, then each character must occur
  an even number of times.
* If the palindrome length is an odd number, then the palindrome is made up of
  characters that occur an even number of times, plus a single character that
  occurs only once (i.e. in the center)

Thus in order to find out the length of the palindrome, we have to find out
how many times each character occurs in the input string. Thgen the length is
the sum of the even number of characters plus 0 or 1, depending on whether there
are single character that do not appear in a pair.

```rust
const BUFFER_LEN: usize = (b'z' - b'A' + 1) as usize;

pub fn longest_palindrome<S: AsRef<str>>(s: S) -> i32 {
    let mut count = [0u8; BUFFER_LEN];
    let mut single_chars = 0;
    let mut paired_chars = 0;

    for ch in s.as_ref().as_bytes().iter().copied() {
        let index = (ch - b'A') as usize;

        // Truncate the count to 0-1, because we don't care how many 
        // times this character occurs, but how many pairs there are.
        // * 0 means that this character appears ina pair
        // * 1 means that this character does NOT appear in a pair (yet)
        count[index] = (count[index] + 1) & 1;
        match count[index] {
            0 => {
                // a previously single character was paired
                single_chars -= 1;
                paired_chars += 2;
            }
            1 => {
                // a new unpaired character
                single_chars += 1;
            }
            _ => unreachable!(),
        }
    }

    paired_chars + (single_chars != 0) as i32
}
```

### Counting character frequencies

```rust
const BUFFER_LEN: usize = (b'z' - b'A' + 1) as usize;

pub fn longest_palindrome<S: AsRef<str>>(s: S) -> i32 {
    let mut count = [0u16; BUFFER_LEN];
    for ch in s.as_ref().as_bytes().iter().copied() {
        let index = (ch - b'A') as usize;
        count[index] += 1;
    }

    count.iter().copied().map(|x| (x / 2) * 2).sum::<u16>() as i32
        + count.iter().any(|&x| x & 1 != 0) as i32
}
```
