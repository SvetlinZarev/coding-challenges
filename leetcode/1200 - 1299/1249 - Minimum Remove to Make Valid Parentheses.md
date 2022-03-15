# [1249. Minimum Remove to Make Valid Parentheses](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/)

## Problem

### Description

Given a string s of `(` , `)` and lowercase English characters.

Your task is to remove the minimum number of parentheses (`(` or `)`, in any
positions) so that the resulting parentheses string is valid and return any
valid string.

Formally, a parentheses string is valid if and only if:

* It is the empty string, contains only lowercase characters, or
* It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are
  valid strings, or
* It can be written as `(A)`, where `A` is a valid string.

### Constraints

* `1 <= s.length <= 10^5`
* `s[i]` is either `(` , `)`, or lowercase English letter.

### Examples

```text
Input: s = "lee(t(c)o)de)"
Output: "lee(t(c)o)de"
Explanation: "lee(t(co)de)" , "lee(t(c)ode)" would also be accepted.
```

```text
Input: s = "a)b(c)d"
Output: "ab(c)d"
```

```text
Input: s = "))(("
Output: ""
Explanation: An empty string is also valid.
```

```text
Input: s = "()ab)"
Output: "()ab"
```

## Solutions

### 2-Pass: Using forward/reverse iteration

```rust
const SKIP_CHAR: char = '\u{0000}';
const BRACKET_OPENING: char = '(';
const BRACKET_CLOSING: char = ')';

pub fn min_remove_to_make_valid<S: AsRef<str>>(s: S) -> String {
    let mut chars = s.as_ref().chars().collect::<Vec<_>>();

    let mut opening = 0u32;
    for ch in chars.iter_mut() {
        match *ch {
            BRACKET_OPENING => opening += 1,
            BRACKET_CLOSING if opening == 0 => *ch = SKIP_CHAR,
            BRACKET_CLOSING => opening -= 1,
            _ => {}
        }
    }

    let mut closing = 0u32;
    for ch in chars.iter_mut().rev() {
        match *ch {
            BRACKET_CLOSING => closing += 1,
            BRACKET_OPENING if closing == 0 => *ch = SKIP_CHAR,
            BRACKET_OPENING => closing -= 1,
            _ => {}
        }
    }

    chars.into_iter().filter(|x| *x != SKIP_CHAR).collect()
}
```

### 1-Pass: using a stack to track the positions of the opening brackets

```rust
const SKIP_CHAR: u8 = b'\0';
const BRACKET_OPENING: u8 = b'(';
const BRACKET_CLOSING: u8 = b')';

pub fn min_remove_to_make_valid(mut s: String) -> String {
    // SAFETY: we are working only with ASCII characters, thus it's safe to delete those byte by byte
    let chars = unsafe { s.as_mut_vec() };

    let mut brackets = vec![];
    for (idx, ch) in chars.iter_mut().enumerate() {
        match *ch {
            BRACKET_OPENING => {
                brackets.push(idx);
            }
            BRACKET_CLOSING if brackets.is_empty() => {
                *ch = SKIP_CHAR;
            }
            BRACKET_CLOSING => {
                brackets.pop();
            }
            _ => {}
        }
    }

    // mark for removal all opening brackets without a closing bracket
    for idx in brackets {
        chars[idx] = SKIP_CHAR;
    }

    chars.retain(|ch| *ch != SKIP_CHAR);
    s
}
```