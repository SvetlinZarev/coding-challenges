# [2053. Kth Distinct String in an Array](https://leetcode.com/problems/kth-distinct-string-in-an-array/)

## Problem

A distinct string is a string that is present only once in an array.

Given an array of strings `arr`, and an integer `k`, return the `k`th distinct
string present in `arr`. If there are fewer than `k` distinct strings, return an
empty string "".

Note that the strings are considered in the order in which they appear in the
array.

#### Constraints

* `1 <= k <= arr.length <= 1000`
* `1 <= arr[i].length <= 5`
* `arr[i]` consists of lowercase English letters.

#### Examples

```text
Input: arr = ["d","b","c","b","c","a"], k = 2
Output: "a"
Explanation:
The only distinct strings in arr are "d" and "a".
"d" appears 1st, so it is the 1st distinct string.
"a" appears 2nd, so it is the 2nd distinct string.
Since k == 2, "a" is returned. 
```

```text
Input: arr = ["aaa","aa","a"], k = 1
Output: "aaa"
Explanation:
All strings in arr are distinct, so the 1st string "aaa" is returned.
```

```text
Input: arr = ["a","b","a"], k = 3
Output: ""
Explanation:
The only distinct string is "b". Since there are fewer than 3 distinct strings, we return an empty string "".
```

## Solutions

### Using HashMap

```rust
use std::collections::HashMap;

pub fn kth_distinct(arr: Vec<String>, k: i32) -> String {
    let mut counts = HashMap::new();
    for s in arr.iter() {
        *counts.entry(s).or_insert(0) += 1;
    }

    let mut kth = k;
    for s in arr.iter() {
        if counts[s] == 1 {
            kth -= 1;
            if kth == 0 {
                return s.to_owned();
            }
        }
    }

    "".to_owned()
}
```