# [14. Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)

## Problem

Write a function to find the longest common prefix string amongst an array of
strings.

If there is no common prefix, return an empty string "".

#### Constraints

* `1 <= strs.length <= 200`
* `0 <= strs[i].length <= 200`
* `strs[i]` consists of only lower-case English letters.

#### Examples

```text
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

```text
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

## Solution

### Vertical scanning

```rust
  pub fn longest_common_prefix(strs: Vec<String>) -> String {
    let mut idx = 0;
    'iter: while idx < strs[0].len() {
        let v = strs[0].as_bytes()[idx];
        for s in strs.iter().skip(1) {
            if idx >= s.len() || s.as_bytes()[idx] != v {
                break 'iter;
            }
        }
        idx += 1;
    }

    strs[0][..idx].to_owned()
}
```