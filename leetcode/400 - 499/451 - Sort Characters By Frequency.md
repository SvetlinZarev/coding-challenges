# [451. Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)

## Problem

Given a string `s`, sort it in decreasing order based on the frequency of the
characters. The frequency of a character is the number of times it appears in
the string.

Return the sorted string. If there are multiple answers, return any of them.

#### Constraints

* `1 <= s.length <= 5 * 10^5`
* `s` consists of uppercase and lowercase English letters and digits.

#### Examples

```text
Input: s = "tree"
Output: "eert"
Explanation: 'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```

```text
Input: s = "cccaaa"
Output: "aaaccc"
Explanation: Both 'c' and 'a' appear three times, so both "cccaaa" and "aaaccc" are valid answers.
Note that "cacaca" is incorrect, as the same characters must be together.
```

```text
Input: s = "Aabb"
Output: "bbAa"
Explanation: "bbaA" is also a valid answer, but "Aabb" is incorrect.
Note that 'A' and 'a' are treated as two different characters.
```

## Solution

```rust
use std::cmp::Ordering;
use std::collections::HashMap;

pub fn frequency_sort(s: String) -> String {
    // count the frequency of each character
    let mut freq = HashMap::with_capacity(255);
    s.chars().for_each(|ch| {
        freq.entry(ch).and_modify(|f| *f += 1).or_insert(1);
    });

    // sort by frequency
    let mut chars = freq.into_iter().map(|(k, v)| (v, k)).collect::<Vec<_>>();
    chars.sort_unstable_by(|a, b| b.cmp(a));

    // reconstruct the string, based on the frequencies
    let mut r = String::with_capacity(s.len());
    for (count, ch) in chars.into_iter() {
        for _ in 0..count {
            r.push(ch);
        }
    }

    r
}
```