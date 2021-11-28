# [917. Reverse Only Letters](https://leetcode.com/problems/reverse-only-letters/)

## Problem

Given a string `s`, reverse the string according to the following rules:

* All the characters that are not English letters remain in the same position.
* All the English letters (lowercase or uppercase) should be reversed.

Return `s` after reversing it.

#### Constraints

* `1 <= s.length <= 100`
* `s` consists of characters with ASCII values in the range `[33, 122]`.
* `s` does not contain `\"` or `\\`.

#### Examples

```text
Input: s = "ab-cd"
Output: "dc-ba"
```

```text
Input: s = "a-bC-dEf-ghIj"
Output: "j-Ih-gfE-dCba"
```

```text
Input: s = "Test1ng-Leet=code-Q!"
Output: "Qedo1ct-eeLg=ntse-T!"
```

## Solution

```rust
pub fn reverse_only_letters(mut s: String) -> String {
    let string = unsafe { s.as_bytes_mut() };

    let mut a = 0;
    let mut b = string.len() - 1;

    loop {
        while a < b && !string[a].is_ascii_alphabetic() {
            a += 1;
        }

        while a < b && !string[b].is_ascii_alphabetic() {
            b -= 1;
        }

        if a >= b {
            break;
        }

        string.swap(a, b);
        a += 1;
        b -= 1;
    }

    s
}
```