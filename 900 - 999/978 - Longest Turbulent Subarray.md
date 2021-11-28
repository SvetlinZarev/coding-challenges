# [978. Longest Turbulent Subarray](https://leetcode.com/problems/longest-turbulent-subarray/)

## Problem

Given an integer array `arr`, return the length of a maximum size turbulent
subarray of `arr`.

A subarray is turbulent if the comparison sign flips between each adjacent pair
of elements in the subarray.

More formally, a subarray `[arr[i], arr[i + 1], ..., arr[j]]` of `arr` is said
to be turbulent if and only if:

* For `i <= k < j`:
    * `arr[k] > arr[k + 1]` when `k` is odd, and
    * `arr[k] < arr[k + 1]` when `k` is even.
* Or, for `i <= k < j`:
    * `arr[k] > arr[k + 1]` when `k` is even, and
    * `arr[k] < arr[k + 1]` when `k` is odd.

#### Constraints

* `1<= arr.length <= 4 * 10^4`
* `0 <= arr[i] <= 10^9`

#### Examples

```text
Input: arr = [9,4,2,10,7,8,8,1,9]
Output: 5
Explanation: arr[1] > arr[2] < arr[3] > arr[4] < arr[5]
```

```text
Input: arr = [4,8,12,16]
Output: 2
```

```text
Input: arr = [100]
Output: 1
```

## Solution

```rust
use std::cmp::Ordering;

fn check(ord: Ordering, a: i32, b: i32) -> (bool, Ordering) {
    let order = a.cmp(&b);
    (
        order == ord && order != Ordering::Equal,
        order.reverse(),
    )
}

pub fn max_turbulence_size(arr: Vec<i32>) -> i32 {
    let mut best = 1;
    let mut count = 1;

    let mut ordering = Ordering::Equal;
    for idx in 1..arr.len() {
        let (matched, next) = check(ordering, arr[idx - 1], arr[idx]);
        if matched {
            count += 1;
        } else {
            best = best.max(count);
            count = if Ordering::Equal == next { 1 } else { 2 }
        }

        ordering = next;
    }

    best.max(count)
}
```