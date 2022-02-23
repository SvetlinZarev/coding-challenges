# [1189. Maximum Number of Balloons](https://leetcode.com/problems/maximum-number-of-balloons/)

## Problem

Given a string `text`, you want to use the characters of `text` to form as many
instances of the word "balloon" as possible.

You can use each character in text at most once. Return the maximum number of
instances that can be formed.

#### Constraints

* `1 <= text.length <= 10^4`
* `text` consists of lower case English letters only.

#### Examples

```text
Input: text = "nlaebolko"
Output: 1
```

```text
Input: text = "loonbalxballpoon"
Output: 2
```

```text
Input: text = "leetcode"
Output: 0
```

## Solution

```rust
const LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn max_number_of_balloons(text: String) -> i32 {
    let mut freq = [0; LETTERS];
    text.as_bytes()
        .iter()
        .copied()
        .map(|ch| ch - b'a')
        .map(|ch| ch as usize)
        .for_each(|ch| {
            freq[ch] += 1;
        });

    freq[(b'b' - b'a') as usize]
        .min(freq[(b'a' - b'a') as usize])
        .min(freq[(b'l' - b'a') as usize] / 2)
        .min(freq[(b'o' - b'a') as usize] / 2)
        .min(freq[(b'n' - b'a') as usize])
}
```