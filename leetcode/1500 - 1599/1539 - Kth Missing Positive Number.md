# [1539. Kth Missing Positive Number](https://leetcode.com/problems/kth-missing-positive-number/)

## Problem

### Description

Given an array `arr` of positive integers sorted in a strictly increasing order,
and an integer `k`.

Find the `k`th positive integer that is missing from this array.

### Constraints

* `1 <= arr.length <= 1000`
* `1 <= arr[i] <= 1000`
* `1 <= k <= 1000`
* `arr[i] < arr[j] for 1 <= i < j <= arr.length`

### Examples

```text
Input: arr = [2,3,4,7,11], k = 5
Output: 9
Explanation: The missing positive integers are [1,5,6,8,9,10,12,13,...]. The 5th missing positive integer is 9.
```

```text
Input: arr = [1,2,3,4], k = 2
Output: 6
Explanation: The missing positive integers are [5,6,7,...]. The 2nd missing positive integer is 6.
```

## Solutions

### Naive implementation

Basically we need to find the last element where the count of missing numbers is
less than `K`. Then we can just find out how many number we need to add to that
number in order to reach the `K`th.

```rust
pub fn find_kth_positive(arr: Vec<i32>, k: i32) -> i32 {
    let mut prev_missing = 0;
    let mut prev_value = 0;

    for idx in 0..arr.len() {
        let mut missing = 0;

        // How many elements are missing between to neighbouring elements in 
        // the array -i.e. - [1,2,3,5] -> 1 element (number 4 is missing)
        if arr[idx] as usize != idx + 1 {
            missing = arr[idx] - prev_value - 1;
        }

        // Once we have skipped past at least K missing elements we can
        // find out which is the Kth element
        if prev_missing + missing >= k {
            return prev_value + k - prev_missing;
        }

        prev_value = arr[idx];
        prev_missing += missing;
    }

    // in case the missing element is outside the array
    arr[arr.len() - 1] + k - prev_missing
}
```

### Binary Search

Here we implement the same idea - find **the last** element where the count of
missing numbers is less than `K`. Then it's easy to calculate the `K`th number
from the last number, `k` and the number of missing elements so far.

```rust
pub fn find_kth_positive(arr: Vec<i32>, k: i32) -> i32 {
    assert!(!arr.is_empty());
    assert!(k > 0);

    let kth = k as usize;
    let mut lo = 0;
    let mut hi = arr.len() - 1;

    // find the last index where `arr[idx] - idx - 1 < k`
    while lo < hi {
        // bias `mid` towards `hi` in order to avoid infinite loops
        // when `mid == lo`, but the condition returns `true`, thus `lo`
        // never changes
        let mid = lo + (hi - lo + 1) / 2;
        let val = arr[mid] as usize;

        if val.saturating_sub(mid + 1) < kth {
            lo = mid;
        } else {
            // avoid underflow when `mid == 0`
            hi = mid.saturating_sub(1);
        }
    }

    // Because the `usize` type cannot go below zero, we have to check if `lo == 0 && arr[0] > k`
    // In that case the Kth element is before that first element in the array
    if lo == 0 && arr[0] > k {
        return k;
    }

    // The expression:
    //
    // let missing_so_far = arr[lo] - lo as i32 - 1;
    // arr[lo] + k - missing_so_far`
    //
    // after simplifying by hand becomes just: `k + lo + 1`
    k + lo as i32 + 1

    // Note that `lo + 1` is actually the first element where 
    // the diff becomes at least K - we can use that to simplify 
    // our solution later
}
```

### Linear scan with iterators

Finding the last such number can also be written with iterators.

```rust
pub fn find_kth_positive(arr: Vec<i32>, k: i32) -> i32 {
    assert!(arr.len() > 0);
    assert!(k > 0);

    let kth = k as usize;
    arr.iter()
        .map(|x| *x as usize)
        .enumerate()
        .take_while(|&(idx, val)| val - idx - 1 < kth)
        .last()
        .map(|(idx, _)| idx as i32)
        .map(|idx| idx + k + 1)
        .unwrap_or(k)
}
```

### Simpler Binary Search

Realising that we are taking the index of the first element where the count of
missing numbers becomes at least `K` we can simplify our binary search to look
for the first occurrence of that number, which is simpler to implement and gets
us rid of the corner case where that number is before the start of the array

```rust
pub fn find_kth_positive(arr: Vec<i32>, k: i32) -> i32 {
    assert!(!arr.is_empty());
    assert!(k > 0);

    let mut lo = 0;
    let mut hi = arr.len();

    while lo < hi {
        let mid = lo + (hi - lo) / 2;
        let val = arr[mid] as usize;

        if val.saturating_sub(mid + 1) >= k as usize {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    hi as i32 + k
}
```