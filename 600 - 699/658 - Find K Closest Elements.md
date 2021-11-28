# [658. Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)

## Problem

Given a sorted integer array `arr`, two integers `k` and `x`, return the `k`
closest integers to `x` in the array. The result should also be sorted in
ascending order.

An integer `a` is closer to `x` than an integer `b` if:

* `|a - x| < |b - x|`, or
* `|a - x| == |b - x|` and `a < b`

#### Constraints

* `1 <= k <= arr.length`
* `1 <= arr.length <= 10^4`
* `arr` is sorted in ascending order.
* `-10^4 <= arr[i], x <= 10^4`

#### Examples

```text
Input: arr = [1,2,3,4,5], k = 4, x = 3
Output: [1,2,3,4]
```

```text
Input: arr = [1,2,3,4,5], k = 4, x = -1
Output: [1,2,3,4]
```

## Solution

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