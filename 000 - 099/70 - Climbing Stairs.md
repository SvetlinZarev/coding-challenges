# [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)

## Problem

You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you
climb to the top?

#### Constraints

* `1 <= n <= 45`

#### Examples

```text
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

```text
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

## Solutions

```rust
pub fn climb_stairs(n: i32) -> i32 {
    match n {
        0 => return 0,
        1 => return 1,
        2 => return 2,
        _ => {}
    }

    let mut dp = vec![0; (n + 1) as usize];
    dp[1] = 1;
    dp[2] = 2;

    for x in 3..=n as usize {
        dp[x] = dp[x - 2] + dp[x - 1];
    }

    dp[n as usize]
}
```

But as we need only the previous two elements of the array, we can use constant
additional space:

```rust
pub fn climb_stairs(n: i32) -> i32 {
    match n {
        0 => return 0,
        1 => return 1,
        2 => return 2,
        _ => {}
    }

    let mut a = 1;
    let mut b = 2;

    for _ in 3..=n as usize {
        let c = a + b;
        a = b;
        b = c;
    }

    b
}
```