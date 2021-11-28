# [1387. Sort Integers by The Power Value](https://leetcode.com/problems/sort-integers-by-the-power-value/)

## Problem

The power of an integer `x` is defined as the number of steps needed to
transform `x` into `1` using the following steps:

* if `x` is even then `x = x / 2`
* if `x` is odd then `x = 3 * x + 1`

For example, the power of `x = 3` is `7` because `3` needs 7 steps to
become `1` (`3 --> 10 --> 5 --> 16 --> 8 --> 4 --> 2 --> 1`).

Given three integers `lo`, `hi` and `k`. The task is to sort all integers in the
interval `[lo, hi]` by the power value in ascending order, if two or more
integers have the same power value sort them by ascending order.

Return the `k`-th integer in the range `[lo, hi]` sorted by the power value.

Notice that for any integer `x` (`lo <= x <= hi`) it is guaranteed that `x` will
transform into 1 using these steps and that the power of `x` is will fit in 32
bit signed integer.

#### Constraints

* `1 <= lo <= hi <= 1000`
* `1 <= k <= hi - lo + 1`

#### Examples

```text
Input: lo = 12, hi = 15, k = 2
Output: 13
Explanation: The power of 12 is 9 (12 --> 6 --> 3 --> 10 --> 5 --> 16 --> 8 --> 4 --> 2 --> 1)
The power of 13 is 9
The power of 14 is 17
The power of 15 is 17
The interval sorted by the power value [12,13,14,15]. For k = 2 answer is the second element which is 13.
Notice that 12 and 13 have the same power value and we sorted them in ascending order. Same for 14 and 15.
```

```text
Input: lo = 1, hi = 1, k = 1
Output: 1
```

```text
Input: lo = 7, hi = 11, k = 4
Output: 7
Explanation: The power array corresponding to the interval [7, 8, 9, 10, 11] is [16, 3, 19, 6, 14].
The interval sorted by power is [8, 10, 11, 7, 9].
The fourth number in the sorted array is 7.
```

```text
Input: lo = 10, hi = 20, k = 5
Output: 13
```

```text
Input: lo = 1, hi = 1000, k = 777
Output: 570
```

## Solution

```rust
use std::collections::{BinaryHeap, HashMap};


pub fn get_kth(lo: i32, hi: i32, k: i32) -> i32 {
    let mut heap = BinaryHeap::with_capacity(k as usize);
    let mut cache = HashMap::with_capacity(((hi - lo) * 3) as usize);

    for v in lo..=hi {
        let pow = power(&mut cache, v);

        if heap.len() < k as usize {
            heap.push((pow, v));
        } else {
            let &(mp, mv) = heap.peek().unwrap();
            if (pow < mp) || (pow == mp && v < mv) {
                heap.pop();
                heap.push((pow, v));
            }
        }
    }

    heap.pop().map(|(_, v)| v).unwrap()
}

fn power(cache: &mut HashMap<i32, i32>, val: i32) -> i32 {
    if (val & (val - 1)) == 0 {
        return val.trailing_zeros() as i32;
    }

    if let Some(&p) = cache.get(&val) {
        return p;
    }

    let next = if val & 1 == 0 { val / 2 } else { val * 3 + 1 };
    let power = 1 + power(cache, next);
    cache.insert(val, power);

    power
}
```