# [473. Matchsticks to Square](https://leetcode.com/problems/matchsticks-to-square/)

## Problem

### Notes

This is the same problem
as [698. Partition to K Equal Sum Subsets](/leetcode/600%20-%20699/698%20-%20Partition%20to%20K%20Equal%20Sum%20Subsets.md)
and can be solved with the same solution.

### Description

You are given an integer array `matchsticks` where `matchsticks[i]` is the
length of the `i`th matchstick. You want to use all the matchsticks to make one
square. You should not break any stick, but you can link them up, and each
matchstick must be used exactly one time.

Return `true` if you can make this square and `false` otherwise.

### Constraints

* `1 <= matchsticks.length <= 15`
* `1 <= matchsticks[i] <= 10^8`

### Examples

![image](resources/473/ex1.jpg)

```text
Input: matchsticks = [1,1,2,2,2]
Output: true
Explanation: You can form a square with length 2, one side of the square came two sticks with length 1.
```

```text
Input: matchsticks = [3,3,3,3,4]
Output: false
Explanation: You cannot find a way to form a square with all the matchsticks.
```

```text
Input: matchsticks = [13, 11, 1, 8, 6, 7, 8, 8, 6, 7, 8, 9, 8]
Output: true
```

```text
Input: matchsticks = [5, 5, 5, 5, 16, 4, 4, 4, 4, 4, 3, 3, 3, 3, 4]
Output: false
```

```text
Input: matchsticks = [14,10,10,10,10,10,10,10,10,10,10,10,8,9,19]
Output: false
```

## Solutions

### DFS

Note: See the solution
for [698. Partition to K Equal Sum Subsets](/leetcode/600%20-%20699/698%20-%20Partition%20to%20K%20Equal%20Sum%20Subsets.md)
because it contains more optimizations and is a lot faster!

```rust
pub fn makesquare(matchsticks: &mut [i32]) -> bool {
    const SIDES: i32 = 4;
    assert!(matchsticks.len() <= 16);

    // We must sort the array in descending order, otherwise the solution 
    // will time-out. The reason is that we always try to put the next matchstick
    // in the first partition first, thus if we try put a longer matchstick, we 
    // will reach the negative conclusion earlier
    matchsticks.sort_unstable_by(|a, b| a.cmp(b).reverse());

    let perimeter = matchsticks.iter().sum::<i32>();
    let side = perimeter / SIDES;
    if perimeter % SIDES != 0 || matchsticks[0] > side {
        return false;
    }

    dfs(matchsticks, side, &mut [0; SIDES as usize], 0)
}

fn dfs<const N: usize>(sticks: &[i32], target: i32, sums: &mut [i32; N], index: usize) -> bool {
    if index == sticks.len() {
        return sums.iter().copied().all(|x| x == target);
    }

    for i in 0..N {
        if sums[i] + sticks[index] > target {
            continue;
        }

        // Not implemented optimisation: skip over sums of equal value

        sums[i] += sticks[index];
        if dfs(sticks, target, sums, index + 1) {
            return true;
        }
        sums[i] -= sticks[index];
    }

    false
}
```

### Backtracking with memoization

```rust
use std::collections::HashSet;

pub fn makesquare(matchsticks: &[i32]) -> bool {
    const SIDES: i32 = 4;
    assert!(matchsticks.len() <= 16);

    let perimeter = matchsticks.iter().sum::<i32>();

    // If the perimeter is not divisible by 4, then it's not possible to
    // make a square, because we can only use whole integers
    if perimeter % SIDES != 0 {
        return false;
    }

    let side_len = perimeter / SIDES;

    // If there are matchsticks longer than the square's side, then
    // it will not be possible to make a square using all matchsticks
    if matchsticks.iter().copied().any(|x| x > side_len) {
        return false;
    }

    let mut failure_cache = HashSet::new();
    backtrack(matchsticks, &mut failure_cache, side_len, 0, SIDES, 0)
}

fn backtrack(
    sticks: &[i32],
    failures: &mut HashSet<u16>,
    target: i32,
    used_sticks: u16,
    mut sides: i32,
    mut current: i32,
) -> bool {
    if failures.contains(&used_sticks) {
        return false;
    }

    if current == target {
        sides -= 1;

        match sides {
            0 => return true,
            _ => current = 0,
        }
    }

    for i in 0..sticks.len() {
        // Skip over any already used sticks
        if used_sticks & 1 << i != 0 {
            continue;
        }

        let next_len = current + sticks[i];

        // The current sum is larger than the square's side,
        // so skip the current match and try the next
        if next_len > target {
            continue;
        }

        let used_sticks = used_sticks | 1 << i;
        if backtrack(sticks, failures, target, used_sticks, sides, next_len) {
            return true;
        }
    }

    failures.insert(used_sticks);
    false
}
```

## Related Problems

* [698. Partition to K Equal Sum Subsets](/leetcode/600%20-%20699/698%20-%20Partition%20to%20K%20Equal%20Sum%20Subsets.md)
