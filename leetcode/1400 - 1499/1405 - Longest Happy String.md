# [1405. Longest Happy String](https://leetcode.com/problems/longest-happy-string/)

## Problem

A string is called happy if it does not have any of the strings 'aaa', 'bbb'
or 'ccc' as a substring.

Given three integers a, b and c, return any string s, which satisfies following
conditions:

* `s` is happy and longest possible.
* `s` contains at most a occurrences of the letter 'a', at most b occurrences of
  the letter 'b' and at most c occurrences of the letter 'c'.
* `s` will only contain 'a', 'b' and 'c' letters.

If there is no such string `s` return the empty string "".

#### Constraints

* `0 <= a, b, c <= 100`
* `a + b + c > 0`

#### Examples

```text
Input: a = 1, b = 1, c = 7
Output: "ccaccbcc"
Explanation: "ccbccacc" would also be a correct answer.
```

```text
Input: a = 2, b = 2, c = 1
Output: "aabbc"
```

```text
Input: a = 7, b = 1, c = 0
Output: "aabaa"
Explanation: It's the only correct answer in this case.
```

## Solution

```rust
use std::collections::BinaryHeap;

pub fn longest_diverse_string(a: i32, b: i32, c: i32) -> String {
    let mut string = String::with_capacity((a + b + c) as usize);
    let mut heap = BinaryHeap::with_capacity(3);

    if a > 0 {
        heap.push((a, 'a'));
    }
    if b > 0 {
        heap.push((b, 'b'));
    }
    if c > 0 {
        heap.push((c, 'c'));
    }

    let mut total = a + b + c;
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

## Related problems

* [984. String Without AAA or BBB](/leetcode/900%20-%20999/984%20-%20String%20Without%20AAA%20or%20BBB.md)