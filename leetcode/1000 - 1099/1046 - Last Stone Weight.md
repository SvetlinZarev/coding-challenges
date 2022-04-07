# [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)

## Problem

### Description

You are given an array of integers `stones` where `stones[i]` is the weight of
the `i`th stone.

We are playing a game with the stones. On each turn, we choose **the heaviest
two stones** and smash them together. Suppose the heaviest two stones have
weights `x` and `y` with `x <= y`. The result of this smash is:

* If `x == y`, both stones are destroyed, and
* If `x != y`, the stone of weight `x` is destroyed, and the stone of weight `y`
  has new weight `y - x`.

At the end of the game, there is at most one stone left.

Return the smallest possible weight of the left stone. If there are no stones
left, return `0`.

### Constraints

* `1 <= stones.length <= 30`
* `1 <= stones[i] <= 1000`

### Examples

```text
Input: stones = [2,7,4,1,8,1]
Output: 1
Explanation: 
We combine 7 and 8 to get 1 so the array converts to [2,4,1,1,1] then,
we combine 2 and 4 to get 2 so the array converts to [2,1,1,1] then,
we combine 2 and 1 to get 1 so the array converts to [1,1,1] then,
we combine 1 and 1 to get 0 so the array converts to [1] then that's the value of the last stone.
```

```text
Input: stones = [1]
Output: 1
```

## Solutions

### Simulation

```rust
use std::collections::BinaryHeap;

#[inline(never)]
pub fn last_stone_weight(stones: Vec<i32>) -> i32 {
    let mut pq = BinaryHeap::from(stones);

    while pq.len() >= 2 {
        let a = pq.pop().unwrap();
        let b = pq.pop().unwrap();

        if a != b {
            pq.push(a.max(b) - a.min(b));
        }
    }

    pq.pop().unwrap_or(0)
}
```