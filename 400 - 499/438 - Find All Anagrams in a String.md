# [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

## Problem

Given two strings `s` and `p`, return an array of all the start indices of `p`'s
anagrams in `s`. You may return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different
word or phrase, typically using all the original letters exactly once.

#### Constraints

* `1 <= s.length, p.length <= 3 * 10^4`
* `s` and `p` consist of lowercase English letters.

#### Examples

```text
Input: s = "cbaebabacd", p = "abc"
Output: [0,6]
Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

```text
Input: s = "abab", p = "ab"
Output: [0,1,2]
Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

## Solution

```rust
pub fn find_anagrams(s: String, p: String) -> Vec<i32> {
    let s = s.as_bytes();
    let p = p.as_bytes();

    // number of distinct characters in `p`
    let mut chars = 0;
    
    // frequency of the characters in `p`
    let mut f = [0; (b'z' - b'a' + 1) as usize];
    for ch in p.iter().copied().map(|b| (b - b'a') as usize) {
        if f[ch] == 0 {
            chars += 1;
        }
        f[ch] += 1;
    }

    let mut from = 0;
    let mut to = 0;
    
    // how many of the distinct characters in `p` can be found
    // in the window [from; to] of `s`
    let mut matched = 0;
    
    // the frequency of the characters in the window [from; to]
    let mut state = [0; (b'z' - b'a' + 1) as usize];

    let mut solution = vec![];

    while to < s.len() {
        let ch = (s[to] - b'a') as usize;
        to += 1;

        // if the window contains a character that is no present
        // in `p` then skip the current window and reset the counters
        if f[ch] == 0 {
            from = to;
            matched = 0;
            state.iter_mut().for_each(|v| *v = 0);
            continue;
        }

        state[ch] += 1;
        if f[ch] == state[ch] {
            matched += 1;
        } else {
            while f[ch] < state[ch] {
                let fch = (s[from] - b'a') as usize;
                if state[fch] == f[fch] {
                    matched -= 1;
                }
                state[fch] -= 1;
                from += 1;
            }
        }

        if matched == chars {
            solution.push(from as i32);
        }
    }

    solution
}
```