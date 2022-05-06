# [1047. Remove All Adjacent Duplicates In String](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/)

## Problem

### Description

You are given a string `s` consisting of lowercase English letters. A duplicate
removal consists of choosing **two adjacent** and equal letters and removing
them.

We repeatedly make duplicate removals on `s` until we no longer can.

Return the final string after all such duplicate removals have been made. It can
be proven that the answer is unique.

### Constraints

* `1 <= s.length <= 10^5`
* `s` consists of lowercase English letters.

### Examples

```text
Input: s = "abbaca"
Output: "ca"
Explanation: 
For example, in "abbaca" we could remove "bb" since the letters are adjacent and equal, and this is the only possible move.  The result of this move is that the string is "aaca", of which only "aa" is possible, so the final string is "ca".
```

```text
Input: s = "azxxzy"
Output: "ay"
```

## Solutions

### Using a stack

```rust
pub fn remove_duplicates<S: AsRef<str>>(s: S) -> String {
    let mut stack = vec![];

    for ch in s.as_ref().chars() {
        match stack.last() {
            None => {
                stack.push(ch);
            }

            Some(&val) if val != ch => {
                stack.push(ch);
            }

            Some(&_val) => {
                stack.pop();
            }
        }
    }

    stack.into_iter().collect()
}
```

## Related Problems

* [1209. Remove All Adjacent Duplicates in String II](/leetcode/1200%20-%201299/1209%20-%20Remove%20All%20Adjacent%20Duplicates%20in%20String%20II.md)
