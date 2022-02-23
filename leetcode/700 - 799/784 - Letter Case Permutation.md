# [784. Letter Case Permutation](https://leetcode.com/problems/letter-case-permutation/)

## Problem

Given a string `s`, we can transform every letter individually to be lowercase
or uppercase to create another string.

Return a list of all possible strings we could create. You can return the output
in any order.

#### Constraints

* `s` will be a string with length between 1 and 12.
* `s` will consist only of letters or digits.

#### Examples

```text
Input: s = "a1b2"
Output: ["a1b2","a1B2","A1b2","A1B2"]
```

```text
Input: s = "3z4"
Output: ["3z4","3Z4"]
```

```text
Input: s = "12345"
Output: ["12345"]
```

```text
Input: s = "0"
Output: ["0"]
```

## Solution

```rust
pub fn letter_case_permutation(s: String) -> Vec<String> {
    let mut result = vec![];
    let mut queue = vec![];

    let s = s.to_ascii_lowercase();
    queue.push((s, 0));

    while let Some((s, from)) = queue.pop() {
        let string = s.as_bytes();

        for idx in from..s.len() {
            if string[idx].is_ascii_alphabetic() {
                let mut permutation = string.to_vec();
                permutation[idx] -= 32; // convert ASCII lowercase to uppercase

                queue.push((unsafe { String::from_utf8_unchecked(permutation) }, idx + 1));
            }
        }

        result.push(s);
    }

    result
}
```