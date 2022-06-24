# [1354. Construct Target Array With Multiple Sums](https://leetcode.com/problems/construct-target-array-with-multiple-sums/)

## Problem

### Description

You are given an array target of `n` integers. From a starting array arr
consisting of `n` 1's, you may perform the following procedure :

* let `x` be the sum of all elements currently in your array.
* choose index `i`, such that `0 <= i < n` and set the value of arr at index `i`
  to `x`.
* You may repeat this procedure as many times as needed.

Return `true` if it is possible to construct the target array from `arr`,
otherwise, return `false`.

### Constraints

* `n == target.length`
* `1 <= n <= 5 * 10^4`
* `1 <= target[i] <= 10^9`

### Examples

```text
Input: target = [9,3,5]
Output: true
Explanation: Start with arr = [1, 1, 1] 
[1, 1, 1], sum = 3 choose index 1
[1, 3, 1], sum = 5 choose index 2
[1, 3, 5], sum = 9 choose index 0
[9, 3, 5] Done
```

```text
Input: target = [1,1,1,2]
Output: false
Explanation: Impossible to create target array from [1,1,1,1].
```

```text
Input: target = [8,5]
Output: true
```

## Solutions

### Going backwards, Priority Queue (Time limit exceeded)

```rust
use std::collections::BinaryHeap;

pub fn is_possible(target: Vec<i32>) -> bool {
    let mut sum = target.iter().sum::<i32>();

    // All 1's -> i.e. this is the initial vector, which we are trying to reach
    let num_elements = target.len();
    if sum as usize == num_elements {
        return true;
    }

    let mut pq = BinaryHeap::from(target);

    while let Some(max_value) = pq.pop() {
        // The maximum value is the sum of all the previous values in the array:
        // [9,3,5] => Max=9, and it is the sum of the previous iteration: [1, 3, 5]
        // So if the maximum is equal to the length of the array, this means that
        // the previous iteration was all ones. Ex: Max=3 => prev = [1,1,1]
        if max_value as usize == num_elements {
            return true;
        }

        // Calculate the value from which the `max_value` originated
        let prev_value = max_value - (sum - max_value);

        // All numbers should be >= 1, so if we have a previous value
        // that is les than 1 ,this means that it's not possible to
        // to create the target array starting from an array containing
        // all 1s
        if prev_value < 1 {
            return false;
        }

        pq.push(prev_value);
        sum = max_value;
    }

    // We always `push()` the same amount of values we `pop()`,
    // thus we will never break from the while loop. The only
    // way to terminate it is to `return` from it.
    unreachable!()
}
```

### Going Backwards, Priority Queue + math

The problem with the previous implementation is that for some large numbers,
such as `[1, 1_000_000_000]`, the algorithm would need to do a billion
iterations, which leads to TLE error.

We can avoid the TLE, by reducing the largest number in 1 step, instead of `n`
steps for as long as it's larger than the `sum - largest`;

Let's have the array `[max, x1, x2]`. Going backwards, this array was
constructed from:

* One step ago `[max - (x1 + x2), x1, x2]`
* Two steps ago: `[max - 2*(x1 + x2), x1, x2]`
* N-steps ago: `[max - n*(x1 + x2), x1, x2]`

So as long as `max` is larger than `x1 + x2` - i.e. `total_sum - max` (or in
other words, as long as `max` is the largest value in the array), we will
be repeatedly subtracting that `sum` from it. Instead of doing it with `N`
steps,
we can do it by using the modulo operation: `let val = max % (total_sum - max)`

**Example**:

* By doing it iteratively, like in the previous
  implementation: `[3, 10] -> [3, 7] -> [3, 4] -> [3, 1] ...`
* By using the modulo operation: `[3, 10] -> [3, 10 % 3] == [3, 1] ...` -> i.e.
  we are able to skip a lot of steps and get the result faster
    * The case where we divide by `mod 1` is a special case -
      i.e. `everything % 1` is 0, thus we assign the divider in place of the 0

```rust
use std::collections::BinaryHeap;

pub fn is_possible(target: Vec<i32>) -> bool {
    let mut sum = target.iter().sum::<i32>();
    // The array contains only 1s
    if sum as usize == target.len() {
        return true;
    }

    let mut pq = BinaryHeap::from(target);

    while let Some(max_value) = pq.pop() {
        if max_value == 1 {
            return true;
        }

        // If max_value is larger than the sum, then the target array cannot be created
        // from all 1s, because the sum is some positive number plus max_value, but there
        // is no such positive number that would make max_sum smaller.
        if max_value >= sum || max_value <= (sum - max_value) {
            return false;
        }

        sum -= max_value;

        let mut prev_value = max_value % sum;
        // In case max_value == sum, relevant for instance when sum == 1
        // from the example `[1, 1000]`
        if prev_value == 0 {
            prev_value = sum;
        }

        sum += prev_value;
        pq.push(prev_value);
    }

    // We always `push()` the same amount of values we `pop()`,
    // thus we will never break from the while loop. The only
    // way to terminate it is to `return` from it.
    unreachable!()
}
```
