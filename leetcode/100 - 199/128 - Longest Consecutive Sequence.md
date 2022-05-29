# [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

## Problem

### Description

Given an unsorted array of integers `nums`, return the length of the longest
consecutive elements sequence.

**You must write an algorithm that runs in `O(n)` time.**

### Constraints

* `0 <= nums.length <= 10^5`
* `-10^9 <= nums[i] <= 10^9`

### Examples

```text
Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```

```text
Input: nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9
```

## Solutions

### `HashSet` + generating the sequence

Although at first sight it appears quadratic, the algorithm actually runs
in `O(n + n)` time which is `O(n)`

```rust
use std::collections::HashSet;

pub fn longest_consecutive(nums: Vec<i32>) -> i32 {
    let numbers = nums.into_iter().collect::<HashSet<_>>();
    let mut longest_sequence = 0;

    for n in numbers.iter().copied() {
        if numbers.contains(&(n - 1)) {
            continue;
        }

        let mut next = n + 1;
        let mut seq_len = 1;

        while numbers.contains(&next) {
            next += 1;
            seq_len += 1;
        }

        longest_sequence = longest_sequence.max(seq_len);
    }

    longest_sequence
}
```

Or another variant of the same idea:

```rust
use std::collections::HashSet;

pub fn longest_consecutive(nums: Vec<i32>) -> i32 {
    let mut numbers = nums.iter().copied().collect::<HashSet<_>>();
    let mut longest_sequence = 0;

    for n in nums.iter().copied() {
        if !numbers.remove(&n) {
            // that number was already processed as part of a sequence
            continue;
        }

        let mut smaller = n - 1;
        while numbers.remove(&smaller) {
            smaller -= 1;
        }

        let mut larger = n + 1;
        while numbers.remove(&larger) {
            larger += 1;
        }

        longest_sequence = longest_sequence.max(larger - smaller - 1);

        // in case there are no more numbers in the set, but there are more number in the array
        if numbers.is_empty() {
            break;
        }
    }

    longest_sequence
}
```