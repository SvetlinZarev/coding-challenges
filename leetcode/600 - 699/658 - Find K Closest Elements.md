# [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)

## Problem

### Description

Given a sorted integer array `arr`, two integers `k` and `x`, return the `k`
closest integers to `x` in the array. The result should also be sorted in
ascending order.

An integer `a` is closer to `x` than an integer `b` if:

* `|a - x| < |b - x|`, or
* `|a - x| == |b - x|` and `a < b`

### Constraints

* `1 <= k <= arr.length`
* `1 <= arr.length <= 10^4`
* `arr` is sorted in ascending order.
* `-10^4 <= arr[i], x <= 10^4`

### Examples

```text
Input: arr = [1,2,3,4,5], k = 4, x = 3
Output: [1,2,3,4]
```

```text
Input: arr = [1,2,3,4,5], k = 4, x = -1
Output: [1,2,3,4]
```

## Solutions

### Using Priority Queue

```rust
use std::collections::BinaryHeap;

pub fn find_closest_elements(arr: Vec<i32>, k: i32, x: i32) -> Vec<i32> {
    let mut pq = BinaryHeap::with_capacity(k as usize);
    for n in arr.into_iter() {
        let d = dist(n, x);
        if pq.len() < pq.capacity() {
            pq.push((d, n));
        } else {
            let &(largest_dist, _) = pq.peek().unwrap();
            if d < largest_dist {
                pq.pop();
                pq.push((d, n));
            }
        }
    }

    let mut result = pq.into_iter().map(|(_, v)| v).collect::<Vec<_>>();
    result.sort_unstable();
    result
}

fn dist(a: i32, b: i32) -> i32 {
    (a - b).abs()
}
```

### Two pointers + Binary Search

Using binary search we find the starting index, and then we expand the slice
either to the left or to the right depending on the given rules:

```rust
pub fn find_closest_elements(arr: Vec<i32>, k: i32, x: i32) -> Vec<i32> {
    assert!(k > 0);
    let k = k as usize;

    assert!(arr.len() >= k);

    // avoid unnecessary copy and processing
    if k == arr.len() {
        return arr;
    }

    let start_idx = match arr.binary_search(&x) {
        Ok(idx) => idx,
        Err(idx) => {
            // if `idx == arr.len()` we must decrement the index, because it's outside of the array
            if idx == arr.len() || (idx != 0 && x - arr[idx - 1] <= arr[idx] - x) {
                idx - 1
            } else {
                idx
            }
        }
    };
    let mut a = start_idx;
    let mut b = start_idx;

    while b - a + 1 < k {
        // We know that `k <= array.len()`, which means that:
        // * If `b == arr.len() - 1`, the `b` pointer cannot expand anymore because it will
        //   go beyond the available array elements, thus in that case it's always safe to
        //   decrement `a`
        // * if `b` does not point at the last cell in the array we must always check that `a` is positive`
        if b == arr.len() - 1 || (a > 0 && x - arr[a - 1] <= arr[b + 1] - x) {
            a -= 1;
        } else {
            b += 1;
        }
    }

    arr[a..=b].to_vec()
}
```