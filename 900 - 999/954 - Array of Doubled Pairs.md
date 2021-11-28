# [954. Array of Doubled Pairs](https://leetcode.com/problems/array-of-doubled-pairs/)

## Problem

Given an integer array of even length `arr`, return `true` if it is possible to
reorder `arr` such that `arr[2 * i + 1] = 2 * arr[2 * i]` for
every `0 <= i < len(arr) / 2`, or `false` otherwise.

#### Constraints

* `2 <= arr.length <= 3 * 10^4`
* `arr.length` is even.
* `-10^5 <= arr[i] <= 10^5`

#### Examples

```text
Input: arr = [3,1,3,6]
Output: false
```

```text
Input: arr = [2,1,2,6]
Output: false
```

```text
Input: arr = [4,-2,2,-4]
Output: true
Explanation: We can take two groups, [-2,-4] and [2,4] to form [-2,-4,2,4] or [2,4,-2,-4].
```

```text
Input: arr = [1,2,4,16,8,4]
Output: false
```

## Solution

```rust
use std::cell::Cell;
use std::cmp::Ordering;
use std::collections::btree_map::BTreeMap;

#[derive(Eq, PartialEq)]
struct Num(i32);

impl Ord for Num {
    fn cmp(&self, other: &Self) -> Ordering {
        if self.0 < 0 && other.0 < 0 {
            self.0.cmp(&other.0).reverse()
        } else {
            self.0.cmp(&other.0)
        }
    }
}

impl PartialOrd for Num {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

pub fn can_reorder_doubled(arr: Vec<i32>) -> bool {
    let mut freq = BTreeMap::new();
    for v in arr.iter().copied() {
        freq.entry(Num(v))
            .and_modify(|cnt: &mut Cell<_>| *cnt.get_mut() += 1)
            .or_insert(Cell::new(1));
    }

    if let Some(count) = freq.remove(&Num(0)) {
        if count.get() % 2 == 1 {
            return false;
        }
    }

    for (&Num(num), num_count) in freq.iter() {
        let nc = num_count.get();
        if nc == 0 {
            continue;
        }

        match freq.get(&Num(num * 2)) {
            Some(pair_count) => {
                let pc = pair_count.get();
                let pairs = pc.min(nc);

                if nc != pairs {
                    return false;
                }

                num_count.set(0);
                pair_count.set(pc - pairs);
            }

            None => return false,
        }
    }

    true
}
```