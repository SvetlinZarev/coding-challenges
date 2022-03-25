# [1029. Two City Scheduling](https://leetcode.com/problems/two-city-scheduling/)

## Problem

### Description

A company is planning to interview `2n` people. Given the array `costs`
where `costs[i] = [aCosti, bCosti]`, the cost of flying the `i`th person to
city `a` is `aCosti`, and the cost of flying the `i`th person to city `b`
is `bCosti`.

Return the minimum cost to fly every person to a city such that exactly `n`
people arrive in each city.

### Constraints

* `2 * n == costs.length`
* `2 <= costs.length <= 100`
* `costs.length` is even.
* `1 <= aCosti, bCosti <= 1000`

### Examples

```text
Input: costs = [[10,20],[30,200],[400,50],[30,20]]
Output: 110
Explanation: 
The first person goes to city A for a cost of 10.
The second person goes to city A for a cost of 30.
The third person goes to city B for a cost of 50.
The fourth person goes to city B for a cost of 20.

The total minimum cost is 10 + 30 + 50 + 20 = 110 to have half the people interviewing in each city.
```

```text
Input: costs = [[259,770],[448,54],[926,667],[184,139],[840,118],[577,469]]
Output: 1859
```

```text
Input: costs = [[515,563],[451,713],[537,709],[343,819],[855,779],[457,60],[650,359],[631,42]]
Output: 3086
```

## Solutions

### Greedy #1

```rust
pub fn two_city_sched_cost(mut costs: Vec<Vec<i32>>) -> i32 {
    assert_eq!(costs.len() % 2, 0);

    // Sort by the difference in costs. The flights with a bigger difference should come first
    costs.sort_by(|a, b| {
        let diff_a = (a[0] - a[1]).abs();
        let diff_b = (b[0] - b[1]).abs();
        diff_a.cmp(&diff_b).reverse()
    });

    // calculate how much people should go in each city
    let limit = costs.len() / 2;

    // track how many people are in city A and B
    let mut in_city_a = 0;
    let mut in_city_b = 0;

    // The total cost so far
    let mut total = 0;

    for cost in costs {
        // If it's cheaper to go in city A, *OR* if there is no place in city B, go to city A.
        if (cost[0] < cost[1] && in_city_a < limit) || in_city_b == limit {
            in_city_a += 1;
            total += cost[0];
        } else {
            in_city_b += 1;
            total += cost[1];
        }
    }

    total
}
```

### Greedy #2

Since we want to divide the people in two equal groups we can sort the arrays by
the gains to flay a person to city A over city B. Then the first half of the
array will contain the pe people who should go to A, and the other half - the
people who should go to B

```rust
pub fn two_city_sched_cost(mut costs: Vec<Vec<i32>>) -> i32 {
    assert_eq!(costs.len() % 2, 0);

    costs.sort_by(|a, b| {
        let diff_a = a[0] - a[1];
        let diff_b = b[0] - b[1];
        diff_a.cmp(&diff_b)
    });

    let mut total = 0;

    for idx in 0..costs.len() / 2 {
        total += costs[idx][0];
    }
    for idx in costs.len() / 2..costs.len() {
        total += costs[idx][1];
    }

    total
}
```

## DP (O(n^2))

```rust
pub fn two_city_sched_cost(mut costs: Vec<Vec<i32>>) -> i32 {
    assert_eq!(costs.len() % 2, 0);
    let n = costs.len() / 2;

    let mut dp = vec![vec![0; n + 1]; n + 1];
    for idx in 1..=n {
        dp[idx][0] = dp[idx - 1][0] + costs[idx - 1][0]
    }
    for idx in 1..=n {
        dp[0][idx] = dp[0][idx - 1] + costs[idx - 1][1];
    }

    for i in 1..=n {
        for j in 1..=n {
            let a = dp[i - 1][j] + costs[i + j - 1][0];
            let b = dp[i][j - 1] + costs[i + j - 1][1];
            dp[i][j] = a.min(b);
        }
    }

    dp[n][n]
}
```