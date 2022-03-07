# [1654. Minimum Jumps to Reach Home](https://leetcode.com/problems/minimum-jumps-to-reach-home/)

## Problem

### Description

A certain bug's home is on the `x`-axis at position `dst`. Help them get there
from position `0`.

The bug jumps according to the following rules:

* It can jump exactly `f` positions forward (to the right).
* It can jump exactly `b` positions backward (to the left).
* It cannot jump backward twice in a row.
* It cannot jump to any forbidden positions.

The bug may jump forward beyond its home, but it cannot jump to positions
numbered with negative integers.

Given an array of integers `forbidden`, where `forbidden[i]` means that the bug
cannot jump to the position `forbidden[i]`, and integers `f`, `b`, and `dst`,
return the minimum number of jumps needed for the bug to reach its home. If
there is no possible sequence of jumps that lands the bug on position `dst`,
return `-1`.

### Constraints

* `1 <= forbidden.length <= 1000`
* `1 <= f, b, forbidden[i] <= 2000`
* `0 <= dst <= 2000`
* All the elements in `forbidden` are distinct.
* Position `dst` is not forbidden.

### Examples

```text
Input: forbidden = [14,4,18,1,15], f = 3, b = 15, dst = 9
Output: 3
Explanation: 3 jumps forward (0 -> 3 -> 6 -> 9) will get the bug home.
```

```text
Input: forbidden = [8,3,16,6,12,20], f = 15, b = 13, dst = 11
Output: -1
```

```text
Input: forbidden = [1,6,2,14,5,17,4], f = 16, b = 9, dst = 7
Output: 2
Explanation: One jump forward (0 -> 16) then one jump backward (16 -> 7) will get the bug home.
```

## Solutions

### BFS

* Keep track of whether we are coming from a forward/backward jump
* The maximum forward position is unlimited. This will lead to a never
  terminating BFS. Thus, how to limit it ?

```rust
use std::collections::hash_map::Entry;
use std::collections::HashMap;

const START_POS: i32 = 0;
const NO_PATH_FOUND: i32 = -1;

pub fn minimum_jumps(forbidden: Vec<i32>, f: i32, b: i32, dst: i32) -> i32 {
    let max_forward = forbidden.iter().copied().max().unwrap_or(0) + f + b + dst;

    // There is no difference between the visited and the forbidden nodes,
    // so put them in one place in order to reduce the number of checks in
    // the code
    //
    // A value of "true" means that we have visited this node with the ability
    // to jump both backwards and forwards. A value of "false" means that we
    // visited the node only with the ability to jump "forward", thus we may need
    // to revisit it again in case we become able to jump back from it.
    let mut visited = HashMap::new();
    for pos in forbidden.into_iter() {
        visited.insert(pos, true);
    }
    visited.insert(START_POS, true);

    let mut queue = vec![];
    let mut neighbours = vec![];

    let mut dist = 0;
    queue.push((START_POS, true));

    while !queue.is_empty() {
        while let Some((pos, can_jump_back)) = queue.pop() {
            if pos == dst {
                return dist;
            }

            let forward = pos + f;
            if forward < max_forward {
                let push = match visited.entry(forward) {
                    // if it was visited with a "can_jump_back == false, we have to enqueue it again
                    Entry::Occupied(mut e) => !e.insert(true),
                    Entry::Vacant(e) => *e.insert(true),
                };

                if push {
                    neighbours.push((forward, true));
                }
            }

            let backward = pos - b;
            if can_jump_back && backward > 0 {
                let push = match visited.entry(backward) {
                    // we have already processed the backward jump
                    Entry::Occupied(_) => false,
                    Entry::Vacant(e) => !*e.insert(false),
                };

                if push {
                    neighbours.push((backward, false));
                }
            }
        }

        std::mem::swap(&mut queue, &mut neighbours);
        dist += 1;
    }

    NO_PATH_FOUND
}
```

## Related Problems

* [1306. Jump Game III](/leetcode/1300%20-%201399/1306%20-%20Jump%20Game%20III.md)