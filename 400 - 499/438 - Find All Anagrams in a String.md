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
    // As we are working with ASCII lets use byte arrays so we can benefit from direct indexing
    let s = s.as_bytes();
    let p = p.as_bytes();

    // Count the number of unique characters in P
    // Find the count (frequency) of each charcter in P
    let mut chars = 0;
    let mut f = [0; (b'z' - b'a' + 1) as usize];
    for ch in p.iter().copied().map(|b| (b - b'a') as usize) { // map ASCII to 0-based numbers
        if f[ch] == 0 {
            chars += 1;
        }
        f[ch] += 1;
    }

    // The two pointers that form our sliding window
    let mut from = 0;
    let mut to = 0;

    // Track how many times we've reached the expected frequency of the unique characters within the sliding window
    let mut matched = 0;

    // Track the frequency of each character within the sliding window
    let mut state = [0; (b'z' - b'a' + 1) as usize];

    // Self explanatory ;)
    let mut solution = vec![];

    // Expand the end of the sliding window until we've processed all characters in S
    while to < s.len() {
        let ch = (s[to] - b'a') as usize;
        to += 1;

        // If this character is not found in P, then move the begining of the sliding window
        // to the next character, because there are no anagrams in it. And because the length of
        //the sliding window becomes 0 we have to reset all counters
        if f[ch] == 0 {
            from = to;
            matched = 0;
            state.iter_mut().for_each(|v| *v = 0);
            continue;
        }

        // Increment the frequency of the current character
        state[ch] += 1;

        // If we reached the expected frequency, then increase the "matched" counter
        // If we've encountered this character too many times, then shrink the sliding window
        // by moving its start towards its end untill the frequency of the current char becomes 
        // equal to the expected frequency
        if f[ch] == state[ch] {
            matched += 1;
        } else {
            while f[ch] < state[ch] {
                let fch = (s[from] - b'a') as usize;

                // By shrinking the window, we may decrease the frequency of some other characters, so 
                // we have to make sure that we decrease the "matched" counter if needed
                if state[fch] == f[fch] {
                    matched -= 1;
                }

                state[fch] -= 1;
                from += 1;
            }
        }

        // if the sliding window contains the correct frequencies of all characters in P, 
        // then add its start to solutions vector
        if matched == chars {
            solution.push(from as i32);
        }
    }

    solution
}
```

## Related Problems

* [567. Permutation in String](500%20-%20599/567%20-%20Permutation%20in%20String.md)