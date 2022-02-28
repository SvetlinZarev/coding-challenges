# [1502. Can Make Arithmetic Progression From Sequence](https://leetcode.com/problems/can-make-arithmetic-progression-from-sequence/)

## Problem

### Description

A sequence of numbers is called an arithmetic progression if the difference
between any two consecutive elements is the same.

Given an array of numbers `arr`, return `true` if the array can be rearranged to
form an arithmetic progression. Otherwise, return `false`.

### Constraints

* `2 <= arr.length <= 1000`
* `-10^6 <= arr[i] <= 10^6`

### Examples

```text
Input: arr = [3,5,1]
Output: true
Explanation: We can reorder the elements as [1,3,5] or [5,3,1] with differences 2 and -2 respectively, between each consecutive elements.
```

```text
Input: arr = [1,2,4]
Output: false
Explanation: There is no way to reorder the elements to obtain an arithmetic progression.
```

## Solution

### With iterators

```rust
pub fn can_make_arithmetic_progression(mut arr: Vec<i32>) -> bool {
    arr.sort_unstable();

    arr.windows(2)
        .skip(1)
        .map(|w| w[1] - w[0])
        .try_fold(
            arr[1] - arr[0],
            |acc, val| {
                if acc == val {
                    Some(acc)
                } else {
                    None
                }
            },
        )
        .map_or(false, |_| true)
}
```

### With loops

```rust
pub fn can_make_arithmetic_progression(mut arr: Vec<i32>) -> bool {
    arr.sort_unstable();

    let diff = arr[1] - arr[0];
    for idx in 2..arr.len() {
        if arr[idx] - arr[idx - 1] != diff {
            return false;
        }
    }

    true
}
```