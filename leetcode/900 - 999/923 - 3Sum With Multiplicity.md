# [923. 3Sum With Multiplicity](https://leetcode.com/problems/3sum-with-multiplicity/)

## Problem

### Description

Given an integer array `arr`, and an integer `target`, return the number of
tuples `i`, `j`, `k` such that `i < j < k`
and `arr[i] + arr[j] + arr[k] == target`.

As the answer can be very large, return it modulo `10^9 + 7`.

### Constraints

* `3 <= arr.length <= 3000`
* `0 <= arr[i] <= 100`
* `0 <= target <= 300`

### Examples

```text
Input: arr = [1,1,2,2,3,3,4,4,5,5], target = 8
Output: 20
Explanation: 
Enumerating by the values (arr[i], arr[j], arr[k]):
(1, 2, 5) occurs 8 times;
(1, 3, 4) occurs 8 times;
(2, 2, 4) occurs 2 times;
(2, 3, 3) occurs 2 times.
```

```text
Input: arr = [1,1,2,2,3,3,4,4,5,5], target = 8
Output: 20
Explanation: 
Enumerating by the values (arr[i], arr[j], arr[k]):
(1, 2, 5) occurs 8 times;
(1, 3, 4) occurs 8 times;
(2, 2, 4) occurs 2 times;
(2, 3, 3) occurs 2 times.

Input: arr = [1,1,2,2,2,2], target = 5
Output: 12
Explanation: 
arr[i] = 1, arr[j] = arr[k] = 2 occurs 12 times:
We choose one 1 from [1,1] in 2 ways,
and two 2s from [2,2,2,2] in 6 ways.
```

## Solutions

### 3Sum adaptation

Basically we do a 3-sum on the sorted & deduplicated array. In order to get the
right answer we first count hom many times each number appears

```rust
use std::collections::HashMap;

const MOD: u64 = 10u64.pow(9) + 7;

pub fn three_sum_multi(mut arr: Vec<i32>, target: i32) -> i32 {
    let mut freq = HashMap::new();

    arr.sort_unstable();
    arr.iter().copied().for_each(|x| {
        freq.entry(x).and_modify(|c| *c += 1).or_insert(1u64);
    });
    arr.dedup();

    let mut answer = 0u64;

    for i in 0..arr.len() {
        let x = arr[i];
        let t = target - x;

        let mut j = i;
        let mut k = arr.len() - 1;

        while j <= k {
            let y = arr[j];
            let z = arr[k];
            let sum = y + z;

            if sum > t {
                if k == 0 {
                    // Handle the corner case where all the numbers are the same: [1,1,1,1,1]
                    break;
                }
                k -= 1;
                continue;
            }

            if sum < t {
                j += 1;
                continue;
            }

            // When the sum is equal to the target sum, we have to find out
            // the contribution of those numbers to the answer
            let count = if i < j && j < k {
                // all three numbers are different
                let fx = freq[&x];
                let fy = freq[&y];
                let fz = freq[&z];

                fx * fy * fz
            } else if i == j && j < k {
                // the first two numbers are the same
                let fx = freq[&x];
                let fz = freq[&z];

                fx * (fx - 1) * fz / 2
            } else if i < j && j == k {
                // the last two numbers are the same
                let fx = freq[&x];
                let fy = freq[&y];

                fx * fy * (fy - 1) / 2
            } else {
                // all numbers are the same
                let f = freq[&x];

                // because the integers are rounded down we have to do all multiplications first
                // (f / 2) * ((f - 1) / 2) * ((f - 2) / 2)
                f * (f - 1) * (f - 2) / 6
            };

            answer = (answer + count) % MOD;

            if k == 0 {
                // Handle the corner case where all the numbers are the same: [1,1,1,1,1]
                break;
            }
            j += 1;
            k -= 1;
        }
    }

    answer as i32
}
```

## Related Problems

* [167. Two Sum II - Input Array Is Sorted](/leetcode/100%20-%20199/167%20-%20Two%20Sum%20II%20-%20Input%20array%20is%20sorted.md)
* [15. 3Sum](/leetcode/000%20-%20099/15%20-%203Sum.md)