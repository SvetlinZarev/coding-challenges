# [77. Combinations](https://leetcode.com/problems/combinations/)

## Problem

Given two integers `n` and `k`, return all possible combinations of `k` numbers
out of the range `[1, n]`.

You may return the answer in any order.

#### Constraints

* `1 <= n <= 20`
* `1 <= k <= n`

#### Examples

```text
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

```text
Input: n = 1, k = 1
Output: [[1]]
```

## Solution

```rust
pub fn combine(n: i32, k: i32) -> Vec<Vec<i32>> {
    // calculate the number of combinations. We'll use that to preallocate
    // enough memory in order to avoid re-allocations later
    let number_of_combinations = combinations(n, k);

    // here we'll store the incomplete combinations (need more elements until
    // they become with a length of K).
    let mut queue = Vec::with_capacity(number_of_combinations);

    // here we'll store the complete combinations (i.e. no more elements to add)
    let mut result = Vec::with_capacity(number_of_combinations);

    // seed the queue with all starting numbers. There is no need to
    // include the numbers which will not lead to a combination.
    // For instance if N=4; K=3, there is no point in starting a seed with 3,
    // because there are no enough numbers to fill an array of length 3 using
    // the range [3;4]
    for seed in 1..=n - k + 1 {
        // we know that all combinations must be of length K, so we can
        // preallocate the required amount of memory in order to
        // avoid re-allocations later
        let mut v = Vec::with_capacity(k as usize);
        v.push(seed);
        queue.push(v);
    }

    if k == 1 {
        return queue;
    }

    let mut buffer_pool = None;

    while let Some(mut v) = queue.pop() {
        let last = v.last().copied().unwrap();

        for x in last + 1..=(n - (k - v.len() as i32) + 1) {
            let mut combination = match buffer_pool.take() {
                // preallocate a copy with the right size. Calling `.clone()` will
                // make a copy with `capacity() == len()` which will immediately cause a
                // reallocation due to the push (Rust 1.55)
                None => Vec::with_capacity(v.capacity()),

                // Given the current implementation, the last popped buffer
                // is thrown away, so we can reuse it here
                Some(buf) => buf,
            };

            combination.clone_from(&v);
            combination.push(x);

            // Self explanatory: if we've made a combination of length K,
            // then push it to the result, otherwise, continue with filling it up
            if combination.len() == k as usize {
                result.push(combination);
            } else {
                queue.push(combination);
            }
        }

        //reuse the buffer
        v.clear();
        buffer_pool = Some(v);
    }

    result
}

fn combinations(n: i32, k: i32) -> usize {
    let nf: usize = (1..(n + 1) as usize).product();
    let kf: usize = (1..(k + 1) as usize).product();
    let nkf: usize = (1..(n - k + 1) as usize).product();

    nf / (kf * nkf)
}
```