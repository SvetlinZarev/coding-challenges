# [1663. Smallest String With A Given Numeric Value](https://leetcode.com/problems/smallest-string-with-a-given-numeric-value/)

## Problem

### Description

The numeric value of a lowercase character is defined as its position (
1-indexed) in the alphabet, so the numeric value of `a` is 1, the numeric value
of `b` is 2, the numeric value of `c` is 3, and so on.

The numeric value of a string consisting of lowercase characters is defined as
the sum of its characters' numeric values. For example, the numeric value of the
string "`abe`" is equal to `1 + 2 + 5 = 8`.

You are given two integers `n` and `k`. Return the **lexicographically
smallest** string with length equal to `n` and numeric value equal to `k`.

Note that a string `x` is lexicographically smaller than string `y` if `x` comes
before `y` in dictionary order, that is, either `x` is a prefix of `y`, or
if `i` is the first position such that `x[i] != y[i]`, then `x[i]` comes
before `y[i]` in alphabetic order.

### Constraints

* `1 <= n <= 10^5`
* `n <= k <= 26 * n`

### Examples

```text
Input: n = 3, k = 27
Output: "aay"
Explanation: The numeric value of the string is 1 + 1 + 25 = 27, and it is the 
smallest string with such a value and length equal to 3.
```

```text
Input: n = 5, k = 73
Output: "aaszz"
```

### Hints

```text
If you build the string from the end to the beginning, it will always be optimal 
to put the highest possible character at the current index.
```

## Solutions

### Greedy (slow & complex solution)

```rust
pub fn get_smallest_string(n: usize, k: usize) -> String {
    assert!(n > 0);
    assert(k >= n);

    let mut answer = vec![];
    let mut remaining = k;

    while remaining > 0 {
        let value = remaining.min((b'z' - b'a' + 1) as usize);
        remaining -= value;
        answer.push(b'a' + value as u8 - 1);
    }

    // fill-in the missing characters
    let mut pos = answer.len() - 1;
    while answer.len() < n {
        while answer[pos] <= b'a' && pos > 0 {
            pos -= 1;
        }

        if answer[pos] <= b'a' {
            panic!(
                "It's not possible to construct a string with length {} representing {}",
                n, k
            );
        }

        answer[pos] -= 1;
        answer.push(b'a');
    }

    answer.reverse();
    String::from_utf8(answer).unwrap()
}
```

### Greedy (simple & fast)

```rust
pub fn get_smallest_string(n: usize, k: usize) -> String {
    assert!(n > 0);
    assert!(k >= n);

    let mut answer = vec![0u8; n];

    // Because we need exactly N characters, we can subtract 
    // all the 1s (i.e. "a"); Thus, we won't need to fill them
    // by going backwards and subtracting from the previous 
    // characters as in the previous solution
    let mut remaining = k - n;
    for ch in answer.iter_mut().rev() { // iterate backwards
        let current = remaining.min((b'z' - b'a') as usize);
        remaining -= current;

        *ch = b'a' + current as u8;
    }

    String::from_utf8(answer).unwrap()
}
```