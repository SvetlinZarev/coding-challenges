# [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/)

## Problem

Given two strings `s1` and `s2`, return `true` if `s2` contains a permutation
of `s1`, or `false` otherwise.

In other words, return `true` if one of `s1`'s permutations is the substring
of `s2`.

#### Constraints

* `1 <= s1.length, s2.length <= 10^4`
* `s1` and `s2` consist of lowercase English letters.

#### Examples

```text
Input: s1 = "ab", s2 = "eidbaooo"
Output: true
Explanation: s2 contains one permutation of s1 ("ba").
```

```text
Input: s1 = "ab", s2 = "eidboaoo"
Output: false
```

## Solution

```rust
const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn check_inclusion(s1: String, s2: String) -> bool {
    let s1 = s1.as_bytes();
    let s2 = s2.as_bytes();

    let mut chars = 0;
    let mut freq = [0; LETTERS];
    for ch in s1.iter().copied().map(|b| (b - b'a') as usize) {
        if freq[ch] == 0 {
            chars += 1;
        }

        freq[ch] += 1;
    }

    let mut a = 0;
    let mut b = 0;
    let mut matched = 0;
    let mut state = [0; LETTERS];

    while matched != chars && b < s2.len() {
        let ch = (s2[b] - b'a') as usize;
        if 0 == freq[ch] {
            state.iter_mut().for_each(|v| *v = 0);
            matched = 0;
            a = b + 1;
        } else {
            state[ch] += 1;
            if state[ch] == freq[ch] {
                matched += 1;
            }

            while state[ch] > freq[ch] {
                let x = (s2[a] - b'a') as usize;
                if state[x] == freq[x] {
                    matched -= 1;
                }
                state[x] -= 1;
                a += 1;
            }
        }

        b += 1;
    }

    matched == chars
}
```

## Related Problems

* [438. Find All Anagrams in a String](/400%20-%20499/438%20-%20Find%20All%20Anagrams%20in%20a%20String.md)