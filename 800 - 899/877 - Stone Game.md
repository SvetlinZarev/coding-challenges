# [877. Stone Game](https://leetcode.com/problems/stone-game/)

## Problem

Alice and Bob play a game with piles of stones. There are an even number of
piles arranged in a row, and each pile has a positive integer number of stones
`piles[i]`.

The objective of the game is to end with the most stones. The total number of
stones across all the piles is odd, so there are no ties.

Alice and Bob take turns, with **Alice starting first**. Each turn, a player
takes the entire pile of stones either from the beginning or from the end of the
row. This continues until there are no more piles left, at which point the
person with the most stones wins.

Assuming Alice and Bob play optimally, return `true` if Alice wins the game, or
`false` if Bob wins.

#### Constraints

* `2 <= piles.length <= 500`
* `piles.length` is even.
* `1 <= piles[i] <= 500`
* `sum(piles[i])` is odd.

#### Examples

```text
Input: piles = [3,7,2,3]
Output: true
```

```text
Input: piles = [5,3,4,5]
Output: true
Explanation: 
Alice starts first, and can only take the first 5 or the last 5.
Say she takes the first 5, so that the row becomes [3, 4, 5].
If Bob takes 3, then the board is [4, 5], and Alice takes 5 to win with 10 points.
If Bob takes the last 5, then the board is [3, 4], and Alice takes 4 to win with 9 points.
This demonstrated that taking the first 5 was a winning move for Alice, so we return true.
```

## Solutions

### Alice always wins

```text
;)
```

### Simulate the game

```rust
pub fn stone_game(piles: Vec<i32>) -> bool {
    assert!(piles.len() >= 2);
    let mut cache = vec![vec![0; piles.len()]; piles.len()];
    let scores_sum = piles.iter().sum::<i32>();
    let best_score = round(&piles, &mut cache, 0, piles.len() - 1);
    best_score > scores_sum / 2
}


fn round(piles: &[i32], cache: &mut Vec<Vec<i32>>, left: usize, right: usize) -> i32 {
    if left >= right {
        return 0;
    }

    if cache[left][right] != 0 {
        return cache[left][right];
    }

    // The player 1 chose left:
    //   => player 2 can chose [left + 1] in which case we can choose [left + 2] or [right]
    //   => player 2 can chose [right] in which case we can choose [left] or [right - 1]
    // Because player 2 will play optimally (i.e. chose the max value) we have to choose min() because that's what's left for us
    let mut best = round(piles, cache, left + 2, right);
    if right > 0 {
        best = best.min(round(piles, cache, left, right - 1));
    }
    let left_sum = piles[left] + best;

    // The player 1 chose right:
    //   => player 2 can chose [left] in which case we can choose [left + 1] or [right]
    //   => player 2 can chose [right] in which case we can choose [left] or [right - 2]
    // Because player 2 will play optimally (i.e. chose the max value) we have to choose min() because that's what left for us
    let mut best = round(piles, cache, left + 1, right);
    if right >= 2 {
        best = best.min(round(piles, cache, left, right - 2));
    }
    let right_sum = piles[right] + best;

    let best = left_sum.max(right_sum); // select out best move
    cache[left][right] = best;
    best
}
```