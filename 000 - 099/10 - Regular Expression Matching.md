# [10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)

## Problem

Given an input string `s` and a pattern `p`, implement regular expression
matching with support for `.` and `*` where:

* `.` Matches any single character.
* `*` Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

#### Constraints

* `1 <= s.length <= 20`
* `1 <= p.length <= 30`
* `s` contains only lowercase English letters.
* `p` contains only lowercase English letters, '.', and '*'.
* It is guaranteed for each appearance of the character '*', there will be a
  previous valid character to match.

#### Examples

```text
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

```text
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

```text
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

```text
Input: s = "mississippi", p = "mis*is*p*."
Output: false
```

## Solution

### Building a state machine

```rust
#[derive(Debug, Copy, Clone, Eq, PartialEq)]
enum Matcher {
    Any,
    RepeatingAny,
    Single(u8),
    Repeating(u8),
}

fn parse_pattern(p: &str) -> Vec<Matcher> {
    let mut pattern = vec![];
    for &x in p.as_bytes() {
        match x {
            b'.' => pattern.push(Matcher::Any),
            b'*' => match pattern.pop() {
                None => panic!("Invalid  (dangling *): {}", p),
                Some(Matcher::Any) => pattern.push(Matcher::RepeatingAny),
                Some(Matcher::Single(ch)) => pattern.push(Matcher::Repeating(ch)),
                Some(pat) => panic!("Star cannot appear after: {:?}", pat),
            },
            ch => pattern.push(Matcher::Single(ch)),
        }
    }

    pattern
}

pub fn is_match(s: String, p: String) -> bool {
    let s = s.as_bytes();
    let p = parse_pattern(&p);

    let mut backtrack = vec![(0, 0)];
    while let Some((mut pi, mut si)) = backtrack.pop() {
        while pi < p.len() && si < s.len() {
            match p[pi] {
                Matcher::Any => {
                    si += 1;
                    pi += 1;
                }

                Matcher::RepeatingAny => {
                    while si < s.len() {
                        backtrack.push((pi + 1, si));
                        si += 1;
                    }

                    pi += 1;
                }

                Matcher::Single(ch) => {
                    if ch != s[si] {
                        break;
                    }

                    si += 1;
                    pi += 1;
                }
                Matcher::Repeating(ch) => {
                    while si < s.len() && ch == s[si] {
                        backtrack.push((pi + 1, si));
                        si += 1;
                    }

                    pi += 1;
                }
            }
        }

        // Consume any leftover optional patterns
        if si >= s.len() {
            while pi < p.len() {
                match p[pi] {
                    Matcher::RepeatingAny | Matcher::Repeating(_) => {
                        pi += 1;
                    }
                    _ => break,
                }
            }
        }

        if si == s.len() && pi == p.len() {
            return true;
        }
    }

    false
}
```