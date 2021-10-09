# [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

## Problem

Given a string `s`, find the length of the longest substring without repeating
characters.

#### Constraints

* `0 <= s.length <= 5 * 104`
* `s` consists of English letters, digits, symbols and spaces.

#### Examples

```text
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

```text
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

```text
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

## Solution

```rust
    pub fn length_of_longest_substring(s: String) -> i32 {
    if s.len() <= 1 {
        return s.len() as i32;
    }

    let data = s.as_bytes();
    let mut state = [0; 256];

    let mut start = 0; // inclusive
    let mut end = 0; // not inclusive
    let mut len = 0;

    loop {
        let ch = data[end] as usize;
        let prev = state[ch];
        state[ch] = end + 1;

        start = start.max(prev);
        end += 1;

        len = len.max(end - start);

        if end >= data.len() {
            break;
        }
    }

    len as i32
}
```