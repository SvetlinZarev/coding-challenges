# [137. Single Number II](https://leetcode.com/problems/single-number-ii/)

## Problem

### Description

Given an integer array `nums` where every element appears three times except for
one, which appears exactly once. Find the single element and return it.

You must implement a solution with a linear runtime complexity and use only
constant extra space.

### Constraints

* `1 <= nums.length <= 3 * 10^4`
* `-2^31 <= nums[i] <= 2^31 - 1`
* Each element in `nums` appears exactly three times except for one element
  which appears once.

### Examples

```text
Input: nums = [2,2,3,2]
Output: 3
```

```text
Input: nums = [0,1,0,1,0,1,99]
Output: 99
```

## Solutions

### Bit Hacks #1

* [Solution @ LeetCode](https://leetcode.com/problems/single-number-ii/discuss/43294/Challenge-me-thx)

```rust
pub fn single_number(nums: Vec<i32>) -> i32 {
    let mut once = 0;
    let mut twice = 0;

    for n in nums {
        once = (once ^ n) & !twice;
        twice = (twice ^ n) & !once;
    }

    once
}
```

#### Algorithm explanation

The idea is that we have two sets:

* `once` - contains elements that have appeared only once
* `twice` - contains elements that have appeared twice

The algorithm works as follows:

* If `N` is not present in `twice`
    * then add it to `once`
    * otherwise remove the element from `once`
* If `N` is not present in `once`
    * then add it to `twice`
    * otherwise remove the element from `twice`

Let's follow the algorithm for the example `[2,2,2,3]`:

1. `once` and `twice` are initialized to 0
2. Loop 1
    1. `(once-is-0 ^ 2) AND (NOT twice-is-0) == 2` => i.e `once` becomes 2
    2. `(twice-is-0 ^ 2) AND (NOT once-is-2) == 0` => i.e. `twice` remains 0
3. Loop 2
    1. `(once-is-2 ^ 2) AND (NOT twice-is-0) == 0` => i.e `once` becomes 0
    2. `(twice-is-0 ^ 2) AND (NOT once-is-0) == 2` => i.e. `twice` becomes 2
4. Loop 3
    1. `(once-is-0 ^ 2) AND (NOT twice-is-2) == 0` => i.e `once` remains 0
    2. `(twice-is-2 ^ 2) AND (NOT once-is-0) == 2` => i.e. `twice` becomes 0
5. Loop 4
    1. `(once-is-0 ^ 3) AND (NOT twice-is-0) == 3` => i.e `once` becomes 3
    2. `(twice-is-0 ^ 3) AND (NOT once-is-3) == 0` => i.e. `twice` remains 0

Thus, at the end `once` is set to 3, which appears only once in the array.

The key here is that the `NOT` operation inverts the bits.
