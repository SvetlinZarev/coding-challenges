# [1306. Jump Game III](https://leetcode.com/problems/jump-game-iii/)

## Problem

### Description

Given an array of non-negative integers `arr`, you are initially positioned
at `start` index of the array. When you are at index `i`, you can jump
to `i + arr[i]` or `i - arr[i]`, check if you can reach to any index with value
of 0.

Notice that you can not jump outside of the array at any time

### Constraints

* `1 <= arr.length <= 5 * 10^4`
* `0 <= arr[i] < arr.length`
* `0 <= start < arr.length`

### Examples

```text
Input: arr = [4,2,3,0,3,1,2], start = 5
Output: true
Explanation: 
All possible ways to reach at index 3 with value 0 are: 
index 5 -> index 4 -> index 1 -> index 3 
index 5 -> index 6 -> index 4 -> index 1 -> index 3 
```

```text
Input: arr = [4,2,3,0,3,1,2], start = 0
Output: true 
Explanation: 
One possible way to reach at index 3 with value 0 is: 
index 0 -> index 4 -> index 1 -> index 3
```

```text
Input: arr = [3,0,2,1,2], start = 2
Output: false
Explanation: There is no way to reach at index 1 with value 0.
```

## Solutions

### BFS

We can treat the array like a graph and do a BFS to check if can reach any node
with a value of 0.

```rust
use std::collections::VecDeque;

pub fn can_reach(mut arr: Vec<i32>, start: usize) -> bool {
    let mut queue = VecDeque::new();
    queue.push_back(start);

    while let Some(position) = queue.pop_front() {
        let value = arr[position];
        if value < 0 {
            continue; //already visited
        }
        arr[position] = -1;

        if value == 0 {
            return true; // target node
        }

        let value = value as usize;
        if position >= value {
            queue.push_back(position - value as usize);
        }
        if position + value < arr.len() {
            queue.push_back(position + value);
        }
    }

    false
}
```

### DFS

The same idea as above, but implemented using DFS

```rust
pub fn can_reach(mut arr: Vec<i32>, start: usize) -> bool {
    let mut stack = vec![];
    stack.push(start);

    while let Some(position) = stack.pop() {
        let value = arr[position];
        if value < 0 {
            continue; //already visited
        }
        arr[position] = -1;

        if value == 0 {
            return true; // target node
        }

        let value = value as usize;
        if position >= value {
            stack.push(position - value as usize);
        }
        if position + value < arr.len() {
            stack.push(position + value);
        }
    }

    false
}
```

## Related Problems

* [55. Jump Game](/leetcode/000%20-%20099/55%20-%20Jump%20Game.md)
* [45. Jump Game II](/leetcode/000%20-%20099/45%20-%20Jump%20Game%20II.md)