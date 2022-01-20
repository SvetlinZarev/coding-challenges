# [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)

## Problem

Koko loves to eat bananas. There are `n` piles of bananas, the `ith` pile has
`piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses
some pile of bananas and eats `k` bananas from that pile. If the pile has less
than `k` bananas, she eats all of them instead and will not eat any more bananas
during this hour.

Koko likes to eat slowly but still wants to finish eating all the bananas before
the guards return.

Return the minimum integer `k` such that she can eat all the bananas within `h`
hours.

#### Constraints

* `1 <= piles.length <= 10^4`
* `piles.length <= h <= 10^9`
* `1 <= piles[i] <= 10^9`

#### Examples

```text
Input: piles = [3,6,7,11], h = 8
Output: 4
```

```text
Input: piles = [30,11,23,4,20], h = 5
Output: 30
```

```text
Input: piles = [30,11,23,4,20], h = 6
Output: 23
```

## Solution

```rust
pub fn min_eating_speed(piles: Vec<i32>, h: i32) -> i32 {
    let mut lo = 1;
    let mut hi = piles.iter().copied().max().unwrap();

    while lo <= hi {
        let speed = (hi - lo) / 2 + lo;
        if possible(&piles, speed, h) {
            hi = speed - 1;
        } else {
            // we failed to eat all bananas, => we need to eat faster
            lo = speed + 1;
        }
    }

    lo
}

fn possible(piles: &[i32], speed: i32, limit: i32) -> bool {
    let mut elapsed = 0;
    for &pile in piles {
        elapsed += pile / speed + (pile % speed != 0) as i32;
    }

    elapsed <= limit
}
```