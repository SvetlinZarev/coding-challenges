# [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

## Problem

Given two strings `s` and `t` of lengths `m` and `n` respectively, return the
minimum window substring of `s` such that every character in `t` (including
duplicates) is included in the window. If there is no such substring, return the
empty string "".

The testcases will be generated such that the answer is unique.

A substring is a contiguous sequence of characters within the string.

#### Constraints

* `m == s.length`
* `n == t.length`
* `1 <= m, n <= 10^5`
* `s` and `t` consist of uppercase and lowercase English letters.

#### Examples

```text
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
```

```text
Input: s = "a", t = "a"
Output: "a"
Explanation: The entire string s is the minimum window.
```

```text
Input: s = "a", t = "aa"
Output: ""
Explanation: Both 'a's from t must be included in the window.
Since the largest window of s only has one 'a', return empty string.
```

## Solution

```rust
pub fn min_window(s: String, t: String) -> String {
    let mut state = [0 as isize; (b'z' - b'A' + 1) as usize];
    let mut target = [0 as isize; (b'z' - b'A' + 1) as usize];
    for b in t.bytes() {
        target[(b - b'A') as usize] += 1;
    }

    let target = target;
    let string = s.as_bytes();

    let mut best_from = 0;
    let mut best_to = 0;

    let mut from = 0;
    let mut to = 0;

    let chars_expected = target.iter().filter(|&&ch| ch > 0).count();
    let mut chars_found = 0;

    while to < string.len() {
        let ch = (string[to] - b'A') as usize;
        if target[ch] > 0 {
            if state[ch] + 1 == target[ch] {
                chars_found += 1;
            }
            state[ch] += 1;
        }

        if chars_found == chars_expected {
            let mut fch = (string[from] - b'A') as usize;
            while target[fch] == 0 || state[fch] > target[fch] {
                if state[fch] > 0 {
                    state[fch] -= 1;
                }

                from += 1;
                fch = (string[from] - b'A') as usize;
            }

            if to - from < best_to - best_from || best_to == 0 {
                best_from = from;
                best_to = to + 1;
            }
        }

        to += 1;
    }

    s[best_from..best_to].to_string()
}
```