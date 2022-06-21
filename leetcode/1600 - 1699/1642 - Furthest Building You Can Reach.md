# [1642. Furthest Building You Can Reach](https://leetcode.com/problems/furthest-building-you-can-reach/)

## Problem

### Description

You are given an integer array `heights` representing the heights of buildings,
some `bricks`, and some `ladders`.

You start your journey from building `0` and move to the next building by
possibly using bricks or ladders.

While moving from building `i` to building `i+1` (0-indexed),

* If the current building's height is greater than or equal to the next
  building's height, you do not need a ladder or bricks.
* If the current building's height is less than the next building's height, you
  can either use one ladder or `(h[i+1] - h[i])` bricks.

Return the furthest building index (0-indexed) you can reach if you use the
given ladders and bricks optimally.

### Constraints

* `1 <= heights.length <= 10^5`
* `1 <= heights[i] <= 10^6`
* `0 <= bricks <= 10^9`
* `0 <= ladders <= heights.length`

### Examples

![image](resources/1642/ex1.gif)

```text
Input: heights = [4,2,7,6,9,14,12], bricks = 5, ladders = 1
Output: 4
Explanation: Starting at building 0, you can follow these steps:
- Go to building 1 without using ladders nor bricks since 4 >= 2.
- Go to building 2 using 5 bricks. You must use either bricks or ladders because 2 < 7.
- Go to building 3 without using ladders nor bricks since 7 >= 6.
- Go to building 4 using your only ladder. You must use either bricks or ladders because 6 < 9.
It is impossible to go beyond building 4 because you do not have any more bricks or ladders.
```

```text
Input: heights = [4,12,2,7,3,18,20,3,19], bricks = 10, ladders = 2
Output: 7
```

```text
Input: heights = [14,3,19,3], bricks = 17, ladders = 0
Output: 3
```

### Hints

> Assume the problem is to check whether you can reach the last building or not.

> You'll have to do a set of jumps, and choose for each one whether to do it
> using a ladder or bricks. It's always optimal to use ladders in the largest
> jumps.

## Solutions

### Greedy with BinaryHeap

```rust
use std::collections::BinaryHeap;

pub fn furthest_building<H: AsRef<[i32]>>(heights: H, bricks: i32, ladders: i32) -> i32 {
    assert!(bricks >= 0);
    assert!(ladders >= 0);

    let heights = heights.as_ref();
    let mut bricks_available = bricks;
    let mut ladders_available = ladders;
    let mut pq = BinaryHeap::new();

    for idx in 1..heights.len() {
        let prev = heights[idx - 1];
        let curr = heights[idx];

        // We don't need bricks or ladders when jumping from a
        // taller building to a shorter/equal one
        if prev >= curr {
            continue;
        }

        let bricks_required = curr - prev;

        // No need to push to the PQ if there are no ladders left, because we 
        // will never be able to `.pop()` from the PQ. This optimisation is optional
        if ladders_available > 0 {
            // Push it now, because we may need to use a ladder on the current index
            pq.push(bricks_required);
        }

        // If we don't have enough bricks, then we have to use a ladder
        if bricks_available < bricks_required {
            // And if we don't have any ladders, then we have to stop
            if ladders_available == 0 {
                return (idx - 1) as i32;
            }

            // But if we had ladders, we should have used it in a place
            // where it would have saved us the biggest amount of bricks
            ladders_available -= 1;
            bricks_available += pq.pop().unwrap();
        }

        bricks_available -= bricks_required;
    }

    // We've managed to move across all buildings
    (heights.len() - 1) as i32
}
```
