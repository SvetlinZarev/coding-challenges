# [151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)

## Problem

Given an input string `s`, reverse the order of the words.

A word is defined as a sequence of non-space characters. The words in `s` will
be separated by at least one space.

Return a string of the words in reverse order concatenated by a single space.

Note that `s` may contain leading or trailing spaces or multiple spaces between
two words. The returned string should only have a single space separating the
words. Do not include any extra spaces.

#### Constraints

* `1 <= s.length <= 10^4`
* `s` contains English letters (upper-case and lower-case), digits, and
  spaces `' '`.
* There is at least one word in `s`.

#### Examples

```text
Input: s = "the sky is blue"
Output: "blue is sky the"
```

```text
Input: s = "  hello world  "
Output: "world hello"
Explanation: Your reversed string should not contain leading or trailing spaces.
```

```text
Input: s = "  Bob    Loves  Alice   "
Output: "Alice Loves Bob"
```

## Solution

```rust
pub fn reverse_words(s: String) -> String {
    let mut result = String::with_capacity(s.len());
    for word in s.rsplit(' ').filter(|w| !w.is_empty()) {
        if !result.is_empty() {
            result.push(' ');
        }

        result.push_str(word);
    }
    result
}
```