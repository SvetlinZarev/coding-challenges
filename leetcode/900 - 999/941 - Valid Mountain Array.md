# [941. Valid Mountain Array](https://leetcode.com/problems/valid-mountain-array/)

## Problem

Given an array of integers `arr`, return `true` if and only if it is a valid
mountain array.

Recall that `arr` is a mountain array if and only if:

* `arr.length >= 3`
* There exists some `i` with `0 < i < arr.length - 1` such that:
    * `arr[0] < arr[1] < ... < arr[i - 1] < arr[i]`
    * `arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`

#### Constraints

* `1 <= arr.length <= 10^4`
* `0 <= arr[i] <= 10^4`

#### Examples

```text
Input: arr = [2,1]
Output: false
```

```text
Input: arr = [3,5,5]
Output: false
```

```text
Input: arr = [0,3,2,1]
Output: true
```

## Solution

```rust
use std::cmp::Ordering;

pub fn valid_mountain_array(arr: Vec<i32>) -> bool {
    if arr.len() < 3 {
        return false;
    }

    if arr[0] > arr[1] {
        return false;
    }

    let mut ascending = true;

    for idx in 1..arr.len() {
        match arr[idx].cmp(&arr[idx - 1]) {
            Ordering::Equal => return false,
            Ordering::Less => {
                if ascending {
                    ascending = false;
                }
            }
            Ordering::Greater => {
                if !ascending {
                    return false;
                }
            }
        }
    }

    !ascending
}
```

or

```rust
use std::cmp::Ordering;

pub fn valid_mountain_array(arr: Vec<i32>) -> bool {
    if arr.len() < 3 {
        return false;
    }

    let mut has_peak = false;
    let mut increasing = true;

    for idx in 1..arr.len() {
        match arr[idx].cmp(&arr[idx - 1]) {
            Ordering::Equal => return false,
            Ordering::Less => {
                if !has_peak {
                    if idx == 1 {
                        return false;
                    }

                    has_peak = true;
                    increasing = false;
                }
            }
            Ordering::Greater => {
                if !increasing {
                    return false;
                }
            }
        }
    }

    has_peak
}
```