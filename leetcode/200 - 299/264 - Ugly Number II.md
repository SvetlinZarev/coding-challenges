# [264. Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)

## Problem

An ugly number is a positive integer whose prime factors are limited to `2`, `3`
, and `5`.

Given an integer `n`, return the `n`th ugly number.

#### Constraints

* `1 <= n <= 1690`

#### Examples

```text
Input: n = 10
Output: 12
Explanation: [1, 2, 3, 4, 5, 6, 8, 9, 10, 12] is the sequence of the first 10 ugly numbers.
```

```text
Input: n = 1
Output: 1
Explanation: 1 has no prime factors, therefore all of its prime factors are limited to 2, 3, and 5.
```

## Solution

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

pub fn nth_ugly_number(mut n: i32) -> i32 {
    let mut heap = BinaryHeap::with_capacity((n * 3) as usize);
    heap.push(Reverse(1i32));

    let mut result = 0;
    while let Some(Reverse(min)) = heap.pop() {
        if min <= result {
            continue;
        }

        result = min;

        n -= 1;
        if n == 0 {
            break;
        }

        if let Some(v) = min.checked_mul(2) {
            heap.push(Reverse(v));

            if let Some(v) = min.checked_mul(3) {
                heap.push(Reverse(v));

                if let Some(v) = min.checked_mul(5) {
                    heap.push(Reverse(v));
                }
            }
        }
    }

    result
}
```

## Related Problems

* [263. Ugly Number](263%20-%20Ugly%20Number.md)
* [313. Super Ugly Number](/leetcode/300%20-%20399/313%20-%20Super%20Ugly%20Number.md)
* [1201. Ugly Number III](/leetcode/1200%20-%201299/1201%20-%20Ugly%20Number%20III.md)
