# [984. String Without AAA or BBB](https://leetcode.com/problems/string-without-aaa-or-bbb/)

## Problem

Given two integers `a` and `b`, return any string `s` such that:

* `s` has length `a + b` and contains exactly `a` 'a' letters, and exactly `b` '
  b' letters,
* The substring 'aaa' does not occur in `s`, and
* The substring 'bbb' does not occur in `s`.

#### Constraints

* `0 <= a, b <= 100`
* It is guaranteed such an `s` exists for the given `a` and `b`.

#### Examples

```text
Input: a = 1, b = 2
Output: "abb"
Explanation: "abb", "bab" and "bba" are all correct answers.
```

```text
Input: a = 4, b = 1
Output: "aabaa"
```

## Solution

```rust
use std::collections::BinaryHeap;

pub fn str_without3a3b(a: i32, b: i32) -> String {
    let mut string = String::with_capacity((a + b) as usize);
    let mut heap = BinaryHeap::with_capacity(3);

    if a > 0 {
        heap.push((a, 'a'));
    }
    if b > 0 {
        heap.push((b, 'b'));
    }

    let mut total = a + b;
    let mut last = None;

    while let Some((mut count, ch)) = heap.pop() {
        string.push(ch);
        count -= 1;
        total -= 1;

        if count > (total - count) * 2 {
            count -= 1;
            total -= 1;
            string.push(ch);
        }

        if let Some(last) = last.take() {
            heap.push(last);
        }

        if count > 0 {
            last = Some((count, ch));
        }
    }

    string
}
```

## Related Problems

* [1405. Longest Happy String](/1400%20-%201499/1405%20-%20Longest%20Happy%20String.md)