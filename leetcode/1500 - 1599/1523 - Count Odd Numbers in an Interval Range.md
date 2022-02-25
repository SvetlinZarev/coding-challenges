# [1523. Count Odd Numbers in an Interval Range](https://leetcode.com/problems/count-odd-numbers-in-an-interval-range/)

## Problem

### Description

Given two non-negative integers `low` and `high`. Return the count of odd
numbers between `low` and `high` (inclusive).

### Constraints

* `0 <= low <= high <= 10^9`

### Examples

```text
Input: low = 3, high = 7
Output: 3
Explanation: The odd numbers between 3 and 7 are [3,5,7].
```

```text
Input: low = 8, high = 10
Output: 1
Explanation: The odd numbers between 8 and 10 are [9].
```

## Solution

```rust
pub fn count_odds(low: i32, high: i32) -> i32 {
    let elements = (high - low + 1); // +1 because "high" is inclusive

    if low % 2 != 0 && high % 2 != 0 {
        return elements / 2 + 1;
    }

    elements / 2
}
```