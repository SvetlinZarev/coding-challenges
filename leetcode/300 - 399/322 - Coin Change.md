# [322. Coin Change](https://leetcode.com/problems/coin-change/)

## Problem

### Description

You are given an integer array `coins` representing coins of different
denominations and an integer `amount` representing a total amount of money.

Return the fewest number of coins that you need to make up that amount. If that
amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

### Constraints

* `1 <= coins.length <= 12`
* `1 <= coins[i] <= 2^31 - 1`
* `0 <= amount <= 10^4`

### Examples

```text
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

```text
Input: coins = [2], amount = 3
Output: -1
```

```text
Input: coins = [1], amount = 0
Output: 0
```

## Solutions

### BFS

```rust
use std::collections::VecDeque;

pub fn coin_change(coins: Vec<i32>, amount: i32) -> i32 {
    assert!(amount >= 0);
    assert!(coins.len() > 0);

    if amount == 0 {
        return 0;
    }

    let mut visited = vec![0; (amount + 1) as usize];
    let mut queue = VecDeque::new();
    queue.push_back(0);

    while let Some(value) = queue.pop_front() {
        if value == amount {
            return visited[value as usize];
        }

        for c in coins.iter().copied() {
            let sum = match value.checked_add(c) {
                None => continue,
                Some(x) if x > amount => continue,
                Some(x) => x,
            };

            if visited[sum as usize] != 0 {
                continue;
            }

            queue.push_back(sum);
            visited[sum as usize] = visited[value as usize] + 1;
        }
    }

    -1
}
```