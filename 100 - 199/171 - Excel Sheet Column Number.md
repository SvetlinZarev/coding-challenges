# [171. Excel Sheet Column Number](https://leetcode.com/problems/excel-sheet-column-number/)

## Problem

Given a string `columnTitle` that represents the column title as appear in an
Excel sheet, return its corresponding column number.

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

* `1 <= columnTitle.length <= 7`
* `columnTitle` consists only of uppercase English letters.
* `columnTitle` is in the range `["A", "FXSHRXW"]`.

#### Examples

```text
Input: columnTitle = "A"
Output: 1
```

```text
Input: columnTitle = "AB"
Output: 28
```

```text
Input: columnTitle = "ZY"
Output: 701
```

## Solution

```rust
pub fn title_to_number(column_title: String) -> i32 {
    let mut number = 0;
    for (pow, &ch) in column_title.as_bytes().iter().rev().enumerate() {
        let x = (ch - b'A' + 1) as i32;
        number += x * 26_i32.pow(pow as u32);
    }
    number
}
```