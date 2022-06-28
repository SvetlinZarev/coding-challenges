# [1647. Minimum Deletions to Make Character Frequencies Unique](https://leetcode.com/problems/minimum-deletions-to-make-character-frequencies-unique/)

## Problem

### Description

A string `s` is called "good" if there are no two different characters in `s`
that have the same frequency.

Given a string `s`, return the minimum number of characters you need to delete
to make `s` good.

The frequency of a character in a string is the number of times it appears in
the string. For example, in the string `aab`, the frequency of `a` is 2, while
the frequency of `b` is 1.

### Constraints

* `1 <= s.length <= 10^5`
* `s` contains only lowercase English letters.

### Examples

```text
Input: s = "aab"
Output: 0
Explanation: s is already good.
```

```text
Input: s = "aaabbbcc"
Output: 2
Explanation: You can delete two 'b's resulting in the good string "aaabcc".
Another way it to delete one 'b' and one 'c' resulting in the good string "aaabbc".
```

```text
Input: s = "ceabaacb"
Output: 2
Explanation: You can delete both 'c's resulting in the good string "eabaab".
Note that we only care about characters that are still in the string at the end (i.e. frequency of 0 is ignored).
```

## Solutions

### Using a priority queue (`O(n + k^2 log k)`)

```rust
use std::collections::BinaryHeap;

const ASCII_LEN: usize = (b'z' - b'a' + 1) as usize;

pub fn min_deletions(s: String) -> i32 {
    let mut frequency = [0u32; ASCII_LEN];
    for &ch in s.as_bytes() {
        let ch = (ch - b'a') as usize;
        frequency[ch] += 1;
    }

    let mut f = frequency
        .iter()
        .copied()
        .filter(|&x| x != 0)
        .collect::<BinaryHeap<_>>();

    let mut answer = 0;
    while let Some(top) = f.pop() {
        if let Some(&next) = f.peek() {
            if top == next {
                if top > 1 {
                    f.push(top - 1);
                }

                answer += 1;
            }
        }
    }

    answer
}
```

### Greedy / Sorting (`O(n + k log k)`)

```rust
const ASCII_LEN: usize = (b'z' - b'a' + 1) as usize;

pub fn min_deletions(s: String) -> i32 {
    let mut frequency = [0usize; ASCII_LEN];
    for &ch in s.as_bytes() {
        let ch = (ch - b'a') as usize;
        frequency[ch] += 1;
    }

    // put the largest values first
    frequency.sort_unstable_by(|a, b| a.cmp(b).reverse());

    // The number of characters that need to be deleted
    let mut answer = 0;

    // the highest allowed frequency in the string
    let mut max_freq = frequency[0];

    for idx in 0..frequency.len() {
        // optional: we can break early if there are no more 
        // characters with frequency > 0
        if frequency[idx] == 0 {
            break;
        }

        if frequency[idx] > max_freq {
            answer += frequency[idx] - max_freq;
            frequency[idx] = max_freq;
        }

        // Imagine the string "abc". We have three characters with the 
        // frequencies `[1,1,1]`. After we accept the first frequency (1),
        // the next will have to be smaller, i.e. 0. Then the third frequency
        // will have to be limited to 0,a s we cannot have negative frequencies
        // That's why we use `saturating_sub()`
        max_freq = 0.max(frequency[idx].saturating_sub(1));
    }

    answer as i32
}
```
