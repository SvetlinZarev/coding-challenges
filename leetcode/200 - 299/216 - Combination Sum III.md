# [216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/)

## Problem

### Description

Find all valid combinations of `k` numbers that sum up to `n` such that the
following conditions are true:

* Only numbers 1 through 9 are used.
* Each number is used at most once.

Return a list of all possible valid combinations. The list must not contain the
same combination twice, and the combinations may be returned in any order.

### Constraints

* `2 <= k <= 9`
* `1 <= n <= 60`

### Examples

```text
Input: k = 3, n = 7
Output: [[1,2,4]]
Explanation:
1 + 2 + 4 = 7
There are no other valid combinations.
```

```text
Input: k = 3, n = 9
Output: [[1,2,6],[1,3,5],[2,3,4]]
Explanation:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
There are no other valid combinations.
```

```text
Input: k = 4, n = 1
Output: []
Explanation: There are no valid combinations.
Using 4 different numbers in the range [1,9], the smallest sum we can get is 1+2+3+4 = 10 and since 10 > 1, there are no valid combination.
```

## Solutions

### Backtracking

```rust
pub fn combination_sum3(k: i32, n: i32) -> Vec<Vec<i32>> {
    // If the sum from 1-to-k is greater than N, then there
    // is no solution. The formula to sum the numbers from
    // 1-to-k is: `n * (n + 1) / 2`
    if k * (k + 1) / 2 > n {
        return vec![];
    }

    let mut answer = vec![];
    backtrack(&mut answer, &mut vec![], 1, n, k);
    answer
}

fn backtrack(
    // The answer stores all found solutions
    answer: &mut Vec<Vec<i32>>,
    // The buffer holds the current answer candidate
    buffer: &mut Vec<i32>,
    // From which number to start iterating on the next recursive call
    // We use it to avoid duplicate combinations such as [1,2] and [2,1]
    from: i32,
    // We use the sum to track when we have reached the answer.
    // Every number we add a number to the buffer we subtract it
    // from the sum. Thus when it reaches 0, we have found the answer
    sum: i32,
    // Track how many numbers we need to add to the buffer
    rem: i32,
) {
    // We have reached the limit of numbers (i.e. K)
    if rem == 0 {
        // If we have found a solution, add it to the answers
        if sum == 0 {
            answer.push(buffer.clone());
        }

        return;
    }

    for idx in from..10 {
        // Because we are iterating the numbers in increasing order,
        // if the sum becomes negative, the next numbers cannot possibly
        // be in the answer, thus break out of the loop.
        if sum - idx < 0 {
            break;
        }

        buffer.push(idx);
        backtrack(answer, buffer, idx + 1, sum - idx, rem - 1);
        buffer.pop();
    }
}
```

## Related problems

* [39. Combination Sum](/leetcode/000%20-%20099/39%20-%20Combination%20Sum.md)
* [40. Combination Sum II](/leetcode/000%20-%20099/40%20-%20Combination%20Sum%20II.md)
