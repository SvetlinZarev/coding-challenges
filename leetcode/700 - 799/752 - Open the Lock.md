# [752. Open the Lock](https://leetcode.com/problems/open-the-lock/)

## Problem

### Description

You have a lock in front of you with 4 circular wheels. Each wheel has 10
slots: `0`, `1`, `2`, `3`, `4`, `5`, `6`, `7`, `8`, `9`. The wheels can rotate
freely and wrap around: for example we can turn `9` to be `0`, or `0` to be `9`.
Each move consists of turning one wheel one slot.

The lock initially starts at `0000`, a string representing the state of the 4
wheels.

You are given a list of `deadends` dead ends, meaning if the lock displays any
of these codes, the wheels of the lock will stop turning and you will be unable
to open it.

Given a `target` representing the value of the wheels that will unlock the lock,
return the minimum total number of turns required to open the lock, or `-1` if
it is impossible.

### Constraints

* `1 <= deadends.length <= 500`
* `deadends[i].length == 4`
* `target.length == 4`
* `target` will not be in the list `deadends`.
* `target` and `deadends[i]` consist of digits only.

### Examples

```text
Output: 6
Explanation: 
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".
```

```text
Input: deadends = ["8888"], target = "0009"
Output: 1
Explanation: We can turn the last wheel in reverse to move from "0000" -> "0009".
```

```text
Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1
Explanation: We cannot reach the target without getting stuck.
```

## Solutions

### BFS

We can view this problem as searching for the shortest path in a graph. Each
node of the graph corresponds to a possible combination. each wheel rotation -
to an edge, wher ethe dead-ends are missing nodes.

```rust
const NO_SOLUTION: i32 = -1;
const START: usize = 0;

pub fn open_lock(deadends: Vec<String>, target: String) -> i32 {
    // It's much faster to preallocate the whole range and use and array of numbers instead of a HashSet
    let mut visited = vec![false; 10_000];

    // The dead-ends are no-different that already visited nodes - i.e. we cannot visit them again
    deadends
        .iter()
        .map(|x| x.as_bytes())
        .map(|x| {
            (x[0] - b'0') as u32 * 1000
                + (x[1] - b'0') as u32 * 100
                + (x[2] - b'0') as u32 * 10
                + (x[3] - b'0') as u32 * 1
        })
        .for_each(|dead_end| {
            visited[dead_end as usize] = true;
        });

    let target = target
        .as_bytes()
        .iter()
        .copied()
        .rev()
        .enumerate()
        .fold(0u32, |acc, (idx, val)| {
            acc + ((val - b'0') as u32) * 10u32.pow(idx as u32)
        });

    if visited[START] || visited[target as usize] {
        return NO_SOLUTION;
    }

    let mut queue = vec![];
    let mut next = vec![];

    let mut turns = 0;
    queue.push([0, 0, 0, 0]);
    visited[START] = true;

    while !queue.is_empty() {
        while let Some(value) = queue.pop() {
            if target == to_number(value) {
                return turns;
            }

            // rotate each wheel forward
            for idx in 0..value.len() {
                let mut rotation = value.clone();
                rotation[idx] = (rotation[idx] + 1) % 10;

                let number = to_number(rotation) as usize;
                if !visited[number] {
                    visited[number] = true;
                    next.push(rotation);
                }
            }

            // rotate each wheel backward
            for idx in 0..value.len() {
                let mut rotation = value.clone();
                if rotation[idx] == 0 {
                    rotation[idx] = 9;
                } else {
                    rotation[idx] -= 1;
                }

                let number = to_number(rotation) as usize;
                if !visited[number] {
                    visited[number] = true;
                    next.push(rotation);
                }
            }
        }

        std::mem::swap(&mut queue, &mut next);
        turns += 1;
    }

    NO_SOLUTION
}

const fn to_number(x: [u8; 4]) -> u32 {
    x[0] as u32 * 1000 + x[1] as u32 * 100 + x[2] as u32 * 10 + x[3] as u32
}
```

### Bidirectional search

```rust
use std::collections::HashSet;

const NO_SOLUTION: i32 = -1;
const START: usize = 0;

pub fn open_lock(deadends: Vec<String>, target: String) -> i32 {
    let mut visited = vec![false; 10_000];

    // The dead-ends are no-different that already visited nodes - i.e. we cannot visit them again
    deadends
        .iter()
        .map(|x| x.as_bytes())
        .map(|x| {
            (x[0] - b'0') as u32 * 1000
                + (x[1] - b'0') as u32 * 100
                + (x[2] - b'0') as u32 * 10
                + (x[3] - b'0') as u32 * 1
        })
        .for_each(|dead_end| {
            visited[dead_end as usize] = true;
        });

    let target = target
        .as_bytes()
        .iter()
        .copied()
        .rev()
        .enumerate()
        .fold(0u32, |acc, (idx, val)| {
            acc + ((val - b'0') as u32) * 10u32.pow(idx as u32)
        });

    if visited[START] || visited[target as usize] {
        return NO_SOLUTION;
    }

    if target as usize == START {
        return 0;
    }

    let mut left = HashSet::new();
    let mut right = HashSet::new();
    let mut next = HashSet::new();

    left.insert(START as u32);
    visited[START] = true;

    right.insert(target);
    visited[target as usize] = true;

    let mut turns = 0;
    while !left.is_empty() && !right.is_empty() {
        if left.len() >= right.len() {
            std::mem::swap(&mut left, &mut right);
        };

        for value in left.drain() {
            let value = to_array(value);

            // rotate each wheel forward/backward
            for forward in [true, false] {
                for idx in 0..value.len() {
                    let mut rotation = value.clone();
                    if forward {
                        rotation[idx] = (rotation[idx] + 1) % 10;
                    } else {
                        if rotation[idx] == 0 {
                            rotation[idx] = 9;
                        } else {
                            rotation[idx] -= 1;
                        }
                    }

                    let number = to_number(rotation);
                    if right.contains(&number) {
                        return turns + 1;
                    }

                    if !visited[number as usize] {
                        visited[number as usize] = true;
                        next.insert(number);
                    }
                }
            }
        }

        std::mem::swap(&mut left, &mut next);
        turns += 1;
    }

    NO_SOLUTION
}

const fn to_number(x: [u8; 4]) -> u32 {
    x[0] as u32 * 1000 + x[1] as u32 * 100 + x[2] as u32 * 10 + x[3] as u32
}

const fn to_array(x: u32) -> [u8; 4] {
    [
        ((x / 1000) % 10) as u8,
        ((x / 100) % 10) as u8,
        ((x / 10) % 10) as u8,
        (x % 10) as u8,
    ]
}
```

## Related Problems

* [365. Water and Jug Problem](/leetcode/300%20-%20399/365%20-%20Water%20and%20Jug%20Problem.md)