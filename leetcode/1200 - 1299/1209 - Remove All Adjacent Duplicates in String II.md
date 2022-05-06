# [1209. Remove All Adjacent Duplicates in String II](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/)

## Problem

### Description

You are given a string `s` and an integer `k`, a `k` duplicate removal consists
of choosing `k` adjacent and equal letters from `s` and removing them, causing
the left and the right side of the deleted substring to concatenate together.

We repeatedly make `k` duplicate removals on `s` until we no longer can.

Return the final string after all such duplicate removals have been made. It is
guaranteed that the answer is unique.

**Note:* We remove only exactly `k` chars. Not more than `k`

### Constraints

* `1 <= s.length <= 10^5`
* `2 <= k <= 10^4`
* `s` only contains lower case English letters.

### Examples

```text
Input: s = "abcd", k = 2
Output: "abcd"
Explanation: There's nothing to delete.
```

```text
Input: s = "deeedbbcccbdaa", k = 3
Output: "aa"
Explanation: 
First delete "eee" and "ccc", get "ddbbbdaa"
Then delete "bbb", get "dddaa"
Finally delete "ddd", get "aa"
```

```text
Input: s = "pbbcggttciiippooaais", k = 2
Output: "ps"
```

## Solutions

### Using a stack

```rust
pub fn remove_duplicates<S: AsRef<str>>(s: S, k: i32) -> String {
    let s = s.as_ref();
    let mut stack = vec![];

    for ch in s.chars() {
        match stack.last() {
            None => {
                stack.push((ch, 1));
            }

            Some(&(val, _cnt)) if val != ch => {
                stack.push((ch, 1));
            }

            Some(&(val, cnt)) => { // val == ch
                stack.pop();
                if cnt + 1 != k {
                    stack.push((val, cnt + 1));
                }
            }
        }
    }

    let mut answer = String::new();
    for (ch, cnt) in stack {
        for _ in 0..cnt {
            answer.push(ch);
        }
    }
    answer
}
```
