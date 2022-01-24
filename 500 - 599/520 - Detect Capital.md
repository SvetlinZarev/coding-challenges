# [520. Detect Capital](https://leetcode.com/problems/detect-capital/)

## Problem

We define the usage of capitals in a word to be right when one of the following
cases holds:

* All letters in this word are capitals, like "USA".
* All letters in this word are not capitals, like "leetcode".
* Only the first letter in this word is capital, like "Google".

Given a string word, return true if the usage of capitals in it is right.

#### Constraints

* `1 <= word.length <= 100`
* `word` consists of lowercase and uppercase English letters.

#### Examples

```text
Input: word = "USA"
Output: true
```

```text
Input: word = "FlaG"
Output: false
```

## Solution

### Using a state machine

```rust
#[derive(Debug, Copy, Clone, Eq, PartialEq)]
enum State {
    Initial,
    FirstUppercase,
    Uppercase,
    Lowercase,
}

pub fn detect_capital_use(word: String) -> bool {
    let ascii = word.as_bytes();
    let mut state = State::Initial;

    for &ch in ascii.iter() {
        let uppercase = ch.is_ascii_uppercase();

        match state {
            State::Initial => {
                if uppercase {
                    state = State::FirstUppercase;
                } else {
                    state = State::Lowercase;
                }
            }

            State::FirstUppercase => {
                if uppercase {
                    state = State::Uppercase;
                } else {
                    state = State::Lowercase;
                }
            }

            State::Uppercase => {
                if !uppercase {
                    return false;
                }
            }

            State::Lowercase => {
                if uppercase {
                    return false;
                }
            }
        }
    }

    true
}
```