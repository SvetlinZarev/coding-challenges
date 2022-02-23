# [168. Excel Sheet Column Title](https://leetcode.com/problems/excel-sheet-column-title/)

## Problem

Given an integer `columnNumber`, return its corresponding column title as it
appears in an Excel sheet.

For example:

```text
A -> 1
B -> 2
C -> 3
...
Z -> 26
AA -> 27
AB -> 28
...
```

#### Constraints

* `1 <= columnNumber <= 2^31 - 1`

#### Examples

```text
Input: columnNumber = 1
Output: "A"
```

```text
Input: columnNumber = 28
Output: "AB"
```

```text
Input: columnNumber = 701
Output: "ZY"
```

## Solution

```rust
pub fn convert_to_title(column_number: i32) -> String {
    let mut title = vec![];
    let mut remaining = column_number;

    while remaining > 0 {
        let symbol = (((remaining - 1) % 26) as u8 + b'A') as char;
        remaining = (remaining - 1) / 26;
        title.push(symbol);
    }

    title.into_iter().rev().collect()
}
```

Or more verbose:

```rust
pub fn convert_to_title(column_number: i32) -> String {
    let mut title = vec![];
    let mut remaining = column_number;

    while remaining > 0 {
        let symbol = match remaining % 26 {
            0 => {
                remaining -= 1;
                'Z'
            }
            s => (s as u8 + b'A' - 1) as char,
        };
        remaining /= 26;
        title.push(symbol);
    }

    title.into_iter().rev().collect()
}
```

## Related Problems

* [171. Excel Sheet Column Number](171%20-%20Excel%20Sheet%20Column%20Number.md)